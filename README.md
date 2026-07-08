# Curvature Signature Classifier for 2D Shape Images

This project classifies 2D binary shape silhouettes using geometric features derived from the curvature of each shape's boundary curve. Instead of feeding raw pixels directly into a model, each silhouette is converted into a fixed-length signed curvature signature and used as input to standard machine learning classifiers.

The project is motivated by classical differential geometry. For a plane curve parameterized by arc length, curvature measures how quickly the tangent direction changes along the curve. Since curvature is invariant under translation and rotation, and can be made scale-invariant by normalizing total arc length, it provides a mathematically principled descriptor for shape classification.

## Overview

Given a binary silhouette image, the pipeline:

1. Extracts the outer boundary contour of the shape.
2. Reparameterizes the contour by normalized arc length.
3. Resamples the boundary to a fixed number of points.
4. Computes the signed curvature along the boundary.
5. Aligns the curvature signature to reduce cyclic-shift ambiguity.
6. Uses the curvature signature as a feature vector.
7. Trains and evaluates classifiers on the resulting geometric representations.

The main goal is to test whether curvature-based boundary representations can distinguish shape classes in a clean 2D silhouette dataset.

## Dataset

The project uses the MPEG-7 Shape Silhouette dataset, which contains binary silhouettes across multiple shape classes. The dataset is well suited for this experiment because the images are clean, high-contrast silhouettes with clearly defined boundaries.

The dataset is not included in this repository. Raw images should be placed in:

```text
data/raw/
```

Processed curvature features and labels should be stored in:

```text
data/processed/
```

## Mathematical Background

Let a plane curve be given by

```math
\alpha(t) = (x(t), y(t)).
```

For a regular plane curve, the signed curvature is

```math
\kappa_s(t) =
\frac{x'(t)y''(t) - y'(t)x''(t)}
{(x'(t)^2 + y'(t)^2)^{3/2}}.
```

When the curve is parameterized by arc length, curvature captures the intrinsic bending of the boundary independent of how quickly the curve is traced. In this project, each extracted contour is reparameterized by normalized arc length and resampled to a fixed number of points before curvature is computed.

Each shape is represented by a curvature signature:

```math
(\kappa_1, \kappa_2, \ldots, \kappa_{128}) \in \mathbb{R}^{128}.
```

This vector becomes the feature representation used for classification.

## Method

### 1. Contour Extraction

Each silhouette image is loaded as a binary image, and the outer contour is extracted using OpenCV. The longest external contour is treated as the boundary of the object.

### 2. Arc-Length Reparameterization

The contour is converted into an ordered sequence of 2D points. The cumulative arc length is computed along the boundary, normalized to total length 1, and used to resample the curve at evenly spaced arc-length values.

### 3. Signed Curvature Computation

First and second derivatives of the resampled curve are approximated numerically using finite differences. The signed curvature formula is then applied pointwise to produce a curvature signal along the boundary.

### 4. Cyclic Alignment

Because each boundary is a closed curve, the starting point of the contour is arbitrary. To reduce cyclic-shift ambiguity, each curvature signature is aligned by rolling the vector so that the largest absolute curvature value appears at a fixed index.

### 5. Classification

The curvature signatures are used as feature vectors for machine learning models. The project evaluates:

- k-nearest neighbours
- multilayer perceptron classifier

Models are evaluated using accuracy and confusion-matrix analysis.

## Repository Structure

```text
curvature-signature-classifier/
├── data/
│   ├── raw/              # raw MPEG-7 silhouette images
│   └── processed/        # extracted curvature signatures and labels
├── figures/              # generated plots and visualizations
├── notebooks/            # exploratory notebooks
├── reports/              # short technical report
├── results/              # evaluation outputs
├── src/                  # source code for the pipeline
├── README.md
├── requirements.txt
└── .gitignore
```

## Expected Outputs

The completed project includes:

- contour extraction from binary silhouettes
- arc-length reparameterization and resampling
- signed curvature feature extraction
- curvature-signature dataset construction
- kNN and MLP classification experiments
- accuracy and confusion-matrix evaluation
- plots of example silhouettes and their curvature signatures
- a short technical report explaining the mathematical pipeline and results

## Installation

Clone the repository:

```bash
git clone https://github.com/wiltonmiller/curvature-signature-classifier.git
cd curvature-signature-classifier
```

Create and activate a virtual environment:

```bash
python3 -m venv .venv
source .venv/bin/activate
```

Install dependencies:

```bash
pip install -r requirements.txt
```

## Usage

After placing the MPEG-7 silhouette dataset in `data/raw/`, the pipeline is organized around three stages:

```bash
python src/extract_curvature_features.py
python src/train_classifiers.py
python src/evaluate_models.py
```

Exploratory development and visualizations are stored in `notebooks/`.

## Report

The short technical report is stored in:

```text
reports/
```

The report explains the motivation, mathematical background, curvature-signature pipeline, classification experiments, and results.

## Motivation

This project investigates a simple question:

> Can classical geometric invariants provide useful feature representations for machine learning on shape data?

The curvature signature is not a learned representation. It is a hand-designed geometric descriptor motivated by differential geometry. This makes the project a small test case for comparing mathematical structure with standard machine learning classification methods.
