<p align="left">
  <img src="doc/logo.png" width="30%">
</p>

# DEPRECATED
Note: this repository is no longer supported! The new recommended way of interacting with the Infinity API is via [Infinity Workflows](https://github.com/toinfinityai/infinity-workflows). This repository is preserved for archival purposes only and will most likely not work!


# Infinity API Tutorials

This repo provides various tutorial notebooks and documentation for working with the synthetic data APIs developed by [Infinity AI](https://infinity.ai/).

## VisionFit API [0.3.1]

<p align="center">
  <img src="doc/visionfit_teaser.gif" width="100%">
</p>

The VisionFit API generates synthetic data for applications at the intersection of computer **vision** and **fit**ness. The API allows users to generate videos of realistic avatars performing a variety of exercises. Pixel-perfect labels include everything from semantic segmentation masks and rep counts to 2D and 3D keypoints (with camera matrices!). See the [VisionFit](visionfit) folder for more information.

## SenseFit API [0.2.0]

<p align="center">
  <img src="doc/sensefit_teaser.gif" width="70%">
</p>

The SenseFit API generates synthetic data for applications at the intersection of on-body **sens**ing and **fit**ness, such as motion tracking data captured with IMU sensors. The API currently allows users to generate synthetic angular position data captured at the wrist (e.g. with a smart watch). Ground-truth labels include continuous, per-frame rep counts. See the [SenseFit](sensefit) folder for more information.

## Spills API [0.1.0]

<p align="center">
  <img src="doc/spills_teaser.gif" width="100%">
</p>

The Spills API generates photorealistic videos of spills in various retail, industrial, and workplace environments. The API allows users to generate videos of spills with various configurations like shape, size, and color. Our pixel-perfect labels include standard object detection and localization annotations for the spill and floor, including segmentation masks and bounding boxes. See the [Spills](spills) folder for more information.

## Environment Setup

The tutorial notebooks have a dependency on `infinity-tools` -- a small Python library that wraps the Infinity REST API with various convenience functions and utilities.

Utilities in `infinity-tools` include input parameter sampling/random generation; functions to easily submit, poll, and await jobs; tools to unpack and visualize generated synthetic data; and much more.

Follow the instructions at the [infinity-tools repository](https://github.com/toinfinityai/infinity-tools) to install this package.

## Contact
The VisionFit and SenseFit APIs are brought to you by [Infinity AI](https://infinity.ai/). We're a small team of dedicated engineers who specialize in generating custom synthetic datasets and built these API hoping they would be useful to people like you! Drop us a line at [info@toinfinity.ai](mailto:info@infinity.ai).
