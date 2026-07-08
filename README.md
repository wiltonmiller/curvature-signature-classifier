# Curvature Signature Classifier for 2D Shape Images

This project classifies 2D binary shape silhouettes using geometric features derived from the curvature of each shape's boundary curve. Instead of representing images by raw pixels, each silhouette is converted into a fixed-length signed curvature signature and used as input to standard machine learning classifiers.

The project is motivated by classical differential geometry. For a plane curve parameterized by arc length, curvature measures how quickly the tangent direction changes along the curve. Since curvature is invariant under rigid motions, and can be made scale-invariant by normalizing total arc length, it provides a mathematically principled descriptor for shape classification.

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

The project uses the MPEG-7 Shape Silhouette dataset, consisting of binary shape images across multiple object classes. The dataset is well suited for this experiment because the images are clean silhouettes with clearly defined boundaries.

The dataset is not included in this repository. Raw images should be placed in:

```text
data/raw/
