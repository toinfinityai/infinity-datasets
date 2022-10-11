<p align="left">
  <img src="./assets/logo.png" width="30%">
</p>

# Smart Facility: Inventory Basic Dataset
An open-source synthetic vision dataset for smart inventory applications.

<p align="center">
  <img src="./assets/inventory_teaser.gif" width="90%">
</p>


## Overview
The Smart Facility: Inventory Basic Dataset is a synthetic, open-source dataset for smart inventory applications. It features 30 videos of photorealistic shelves stocked with items. Items vary in identity, number, and orientation. Each video contains items in the same _nominal_ locations with the same _average_ number of items visible (fill factor), but the item orientations and their individual visibility varies by frame. Each video is accompanied by pixel-perfect labels and annotations. This dataset can be used to develop computer vision algorithms to detect the presence, location and identity of items from the perspective of a fixed camera.

## Dataset Features
- 30 videos, 100 frames each
- 1024px x 768px resolution
- A total of 38 body care assets, randomly chosen and placed per video
- A mean shelf fill factor varying from 10% to 100% across videos
- Randomized item orientations and item visibility per video frame
- Segmentation masks and rich annotations per item and item kind
- A variety of camera positions with heights varying from ground-level to ceiling-level

## Getting Started 
Download the dataset from the [Infinity Marketplace](https://marketplace.infinity.ai/products/smart-facility-inventory-basic-dataset), and use the provided [quickstart notebook](quickstart.ipynb) to visualize labels.

## Contents
For each dataset sample, we provide the following files:

- `{video_number}_params.json`: A JSON file describing the parameters used to generate the dataset sample. See [below](#dataset-parameters) for a description of each.
- `{video_number}.mp4`: The RGB video of the inventory on shelves.
- `{video_number}_labels.json`: A COCO-formatted JSON file of video-, frame-, and instance-level annotations and labels. See [below](#Annotations) for a full description of provided labels.
- `{video_number}_segmentation.zip`: A zipped file containing frame-level semantic and instance segmentation masks. 

## Dataset Parameters

The `{video_number}_params.json` file provided for each dataset sample includes the parameters that were used to generate that specific video. For convenience, we provide a description of each parameter below:

- `scene`: Environment/scene identity
- `num_frames`: Number of frames in the video 
- `image_width`: Image width in pixels
- `image_height`: Image height in pixels
- `camera_x`: Camera x position in world coordinates in meters
- `camera_y`: Camera y position in world coordinates in meters
- `camera_z`: Camera height in world coordinates in meters
- `random_seed`: The random seed used to generate the video

Note that while the camera _location_ varies across videos, the focus location does not change.

## Annotations

The `{video_number}_labels.json` file is provided in standard [COCO](https://cocodataset.org/#format-data) format. The third-party [pycocotools](https://pypi.org/project/pycocotools/) package provides useful utility functions for working with the data structure.

### Scene-level annotations
Scene-level annotations are provided for each video. They are accessible via the top-level `info` field of the JSON data structure. Scene-level annotations include:
- `camera_K_matrix`: Intrinsic K matrix of the synthetic camera.
- `camera_P_matrix`: P matrix of the synthetic camera. This can be used to project any 3D position in the global coordinate system onto the image plane. Note that `P = K @ RT`.
- `camera_RT_matrix`: Extrinsic RT matrix of the synthetic camera (rotation + translation).

### Frame-level annotations
Frame-level annotations are provided for each frame of a video. They are accessible via the top-level `images` field of the JSON data structure. Frame-level annotations include:

- `frame_number`: The frame number of the corresponding image.

### Instance-level annotations
Instance-level annotations are provided for the inventory items and all other objects in the scene. Instance-level annotations are accessible via the top-level `annotations` field of the JSON data structure. 

**Note: The instance-level annotations enumerated below are only provided when a non-empty bounding box can be defined.** Annotations for items out of camera field of view are available, but they won't contain these keys.

- `color`: Normalized RGB value in the corresponding instance segmentation masks.
- `bbox`: Bounding box in standard COCO format.
- `segmentation`: Polygon segmentation in standard COCO format.
- `area`: Area enclosed by polygon segmentation.
- `cuboid_coordinates`: Image coordinates (xy) and depth (z) values of the 3D cuboid enclosing the instance, with axes that are parallel to the global coordinate system. The order of the cuboid points is shown below. Negative z values indicate that a point is behind the camera.

### Segmentation annotations

For each frame of a video, the following segmentation masks are provided:

- `image.{frame_number}.cseg.png`: Semantic segmentation. Inventory items of the same kind will be grouped by color.
- `image.{frame_number}.iseg.png`: Instance segmentation.

## Terms and Conditions
This work is licensed under a
[Creative Commons Attribution 4.0 International License](http://creativecommons.org/licenses/by/4.0/). Both academic and commercial applications are allowed.