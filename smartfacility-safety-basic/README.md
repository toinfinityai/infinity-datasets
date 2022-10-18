<p align="left">
  <img src="./assets/logo.png" width="30%">
</p>

# Smart Facility: Safety Basic Dataset
An open-source synthetic vision dataset for workplace safety applications with fixed-camera systems.

<p align="center">
  <img src="./assets/safety_teaser.gif" width="90%">
</p>


## Overview

The Smart Facility: Safety Basic Dataset is a synthetic, open-source dataset for safety applications and smart facility management. It features 15 videos of photorealistic avatars placed within complex warehouse environments, wearing various combinations of personal protective equipment (PPE) and performing different actions related to workplace safety. Each video is accompanied by pixel-perfect labels and annotations, including 2D/3D keypoints, frame-specific activity labels, and segmentation masks for each PPE item. The dataset can be used to develop various computer vision algorithms related to workplace safety for fixed-camera systems, including:

- Human pose/activity classification for smart ergonomics
- PPE detection and classification for safety or risk assessment

## Dataset Features

- 15 videos, 10 seconds each, 12 fps frame rate
- 3 unique background scenes
- 1080px x ~720px resolution (exact height is scene-dependent)
- In-domain action sequences, including lifting and reaching movements with good and bad form
- Complex combinations of personal protective equipment — including hardhats, goggles, respirators, ear protection, and safety vests — with multiple color options available.
- Rich annotations, including 2D/3D keypoints, frame-specific activity labels, and segmentation masks for each PPE item

## Getting Started 
Download the dataset from the [Infinity Marketplace](https://marketplace.infinity.ai/products/smart-facility-safety-basic-dataset), and use the provided [quickstart notebook](quickstart.ipynb) to visualize labels.

## Contents
For each dataset sample, we provide the following files:

- `{video_number}_params.json`: A JSON file describing the parameters used to generate the dataset sample. See [below](#dataset-parameters) for a description of each.
- `{video_number}.mp4`: The RGB video.
- `{video_number}_labels.json`: A COCO-formatted JSON file of frame- and instance-level annotations and labels. See [below](#Annotations) for a full description of provided labels.
- `{video_number}_segmentation.zip`: A zip file containing frame-level semantic and instance segmentation masks. For each avatar, instance segmentations masks are provided with and without occlusion.

## Dataset Parameters

The `{video_number}_params.json` file provided for each dataset sample includes the parameters that were used to generate that specific video. For convenience, we provide a description of each parameter below:

- `scene`: Name of the chosen environment/scene.
- `video_duration`: The total duration of the rendered video, in seconds.
- `frame_rate`: The frame rate of the rendered video.
- `max_dimension`: The size of the video's largest dimension (width or height). This effectively controls the resolution, since different scenes may have different aspect ratios.
- `num_people`: Number of people placed in the scene. In each video, 1 avatar walks through the scene, while all others perform an in-place action sequence.
- `random_seed`: The random seed used to generate the video.

## Annotations

The `{video_number}_labels.json` file is provided in standard [COCO](https://cocodataset.org/#format-data) format. The third-party [pycocotools](https://pypi.org/project/pycocotools/) package provides useful utility functions for working with the data structure.

### Frame-level annotations
Frame-level annotations are provided for each frame of a video. They are accessible via the top-level `images` field of the JSON data structure. Frame-level annotations include:

- `frame_number`: The frame number of the corresponding image.

### Instance-level annotations
Instance-level annotations are provided for each avatar and its corresponding PPE items. Instance-level annotations are accessible via the top-level `annotations` field of the JSON data structure. Instance-level annotations include:

- `color`: Normalized RGB value in the corresponding instance segmentation masks.
- `bbox`: Bounding box in standard COCO format.
- `segmentation`: Polygon segmentation in standard COCO format.
- `area`: Area enclosed by polygon segmentation.
- `cuboid_coordinates`: Image coordinates (xy) and depth (z) values of the 3D cuboid enclosing the instance, with axes that are parallel to the global coordinate system. The order of the cuboid points is shown below. Negative z values indicate that a point is behind the camera.

```
   3-------2
  /|      /|
 / |     / |
0-------1  |
|  7----|--6
| /     | /
4-------5
```

For all annotations associated with an avatar (i.e. belonging to the `person` category), we provide the following additional annotations:

- `person_idx`: Index of the person in the scene. Useful for collecting all annotations across time for a specific avatar.
- `gender`: Gender of the underlying SMPL-X body model.
- `action`: Action that the avatar is performing in a given frame.
- `ppe`: Dictionary indicating which PPE items the avatar is wearing.
- `armature_keypoints`: A data structure including image coordinates (x,y), visibility (v), depth from camera (z, in meters), and 3D position in the global coordinate system (x_global, y_global, z_global; in meters) for each degree-of-freedom in the underlying SMPL-X model. Visibility values indicate whether keypoints are not in the image frame (0), in the image frame but occluded (1), or visible (2). Negative z values indicate that a point is behind the camera.
- `keypoints`: Image coordinates and visibility in standard COCO format for each keypoint in the 17-point COCO skeleton. Visibility labels are defined as in `armature_keypoints`. Note that the hip keypoints in this data structure correspond to different locations than those in `armature_keypoints`. Specifically, they correspond to a more lateral location designed to better reflect where human annotators typically place the hips (e.g. in the COCO dataset).
- `num_keypoints`: Number of keypoints in the COCO skeleton with non-zero visibility.
- `percent_occlusion`: Percentage of the instance that is not visibile due to environmental occlusion (i.e. objects in the foreground). It is quantified as the relative difference between the occluded and unoccluded instance segmentation masks, which are also provided.
- `yaw_rotation_deg`: Rotation of the avatar around the Z-axis in the global coordinate sytem, in degrees. Only provided for static (non-walking) avatars.
- `location`: Location of the avatar in the global coordinate system, in meters. Only provided for static (non-walking) avatars.


### Segmentation annotations

For each frame of a video, the following segmentation masks are provided:

- `image.{frame_number}.cseg.png`: Semantic segmentation.
- `image.{frame_number}.iseg.png`: Instance segmentation.
- `image.{frame_number}.iseg.{annotation_id}.png`: Instance segmentation without occlusion (only provided for `person` annotations).

## Terms and Conditions
This work is licensed under a
[Creative Commons Attribution 4.0 International License](http://creativecommons.org/licenses/by/4.0/). Both academic and commercial applications are allowed.