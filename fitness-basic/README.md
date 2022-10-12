<p align="left">
  <img src="./assets/logo.png" width="30%">
</p>

# Fitness Basic Dataset
An open-source synthetic dataset for fitness and physical therapy applications.

<p align="left">
  <img src="./assets/fitness_basic_teaser.gif">
</p>

## Overview 
The Fitness Basic Dataset is a synthetic, open-source dataset for fitness and physical therapy applications. It includes videos of diverse avatars performing multiple repetitions of common exercises. It includes significant variation in the environment, lighting conditions, avatar demographics, and movement trajectories. From cadence to kinematic trajectory, each rep is done slightly differently — just like real humans. Videos are accompanied by a rich set of pixel-perfect labels and annotations, including frame-specific repetition counts.

## Dataset Features

- 100 videos per exercise, spanning 5 to 10 repetitions each.
- 7 unique indoor scenes.
- Realistic environmental occlusion (+ corresponding labels).
- Diverse lighting conditions.
- Varied body shape, skin tones, and clothing.
- Rich annotations for 2D and 3D supervision.

## Exercises

The dataset includes the following exercises: 

1. Pushups
2. Alternating Bicep Curls (with dumbbells)
2. Delt Flys (with dumbbells)
3. Squats
4. Bird Dogs
5. Supermans
6. Bicycle Crunches
7. Leg Raises
8. Front Raises (with dumbbells)
9. Overhead Press (with dumbbells)

## Getting Started

Download the dataset from the [Infinity Marketplace](https://marketplace.infinity.ai/products/fitness-basic-dataset), and use the provided [quickstart notebook](quickstart.ipynb) to visualize labels and filter the dataset based on provided metadata.

## Contents
For each dataset sample, we provide the following files:

- `{video_number}.mp4`: 224x224 RGB video (24 fps).
- `{video_number}.json`: COCO-formatted JSON file of video-, frame-, and instance-level annotations and labels. See [below](#Annotations) for a full description of provided labels.
- `{video_number}_img_labels.zip`: Zip file of the corresponding semantic and instance segmentation masks (with and without occlusion).

## Annotations

Metadata and annotations for the dataset are packaged at the level of individual videos. The JSON file provided for each video includes annotations in standard [COCO](https://cocodataset.org/#format-data) format. The third-party [pycocotools](https://pypi.org/project/pycocotools/) package provides useful utility functions for working with the data structure.


### Scene-level annotations

Scene-level annotations are provided for each video. They are accessible via the top-level `info` field of the JSON data structure. Scene-level annotations include:

- `camera_pitch`: Pitch of the camera, in degrees. A value of 90 indicates the camera's line of sight is parallel to the ground plane.
- `camera_height`: Height of the camera relative to the floor plane, in meters.
- `avatar_presenting_gender`: Gender of the underlying SMPL-X body model.
- `avatar_attire_top`/`avatar_attire_bottom`: Clothing type used in the applied UV texture.
- `avatar_betas`: 10 shape coefficients for the underlying SMPL-X body model.
- `avatar_waist_circumference`: Circumference of the SMPL-X body model's waist, in meters.

### Frame-level annotations
Frame-level annotations are provided for each frame of a video. They are accessible via the top-level `images` field of the JSON data structure. Frame-level annotations include:

- `frame_numer`: The frame number of the corresponding image.
- `rep_count`: The number of repetitions completed since the beginning of the video PLUS a float in the range of [0,1] that indicates the current frame’s relative position in the exercise sequence. For example, a value of 4.23 indicates that 4 full repetitions have been completed since the beginning of the video, and that the current frame corresponds to 23% completion of the next one.

### Instance-level annotations
Instance-level annotations are provided for every unique object belonging to the `person` or `dumbbell` category. Instance-level annotations are accessible via the top-level `annotations` field of the JSON data structure.

- `color`: Normalized RGB value in the corresponding instance segmentation masks
- `percent_in_fov`: Percentage of the vertices from the underlying mesh that are within the camera's field-of-view, regardless of occlusion status. This value can be used to disambiguate whether sparse instance segmentation masks reflect a high degree of environmental occlusion versus the instance being out-of-frame.
- `percent_occlusion`: Percentage of the instance that is not visibile due to environmental occlusion (i.e. objects in the foreground). It is quantified as the relative difference between the occluded and unoccluded instance segmentation masks, which are also provided.
- `bbox`: Bounding box in standard COCO format
- `segmentation`: Polygon segmentation in standard COCO format
- `area`: Area enclosed by polygon segmentation
- `cuboid_coordinates`: Image coordinates of the surroinding 3D cuboid, with axes that are parallel to the global coordinate system. The order of the cuboid points is shown below.

```
   3-------2
  /|      /|
 / |     / |
0-------1  |
|  7----|--6
| /     | /
4-------5
```

We also provide the following annotations for each `person` instance:

- `armature_keypoints`: Image coordinates (x,y), visibility (v), and depth from camera (z, in meters) for each degree-of-freedom in the underlying SMPL-X model. Visibility values indicate whether keypoints are outside the image frame and not occluded from view (0), inside or outside the image frame and occluded from view (1), or inside the image frame and not occluded from view (2). Note: keypoints that are outside the image frame will have x/y values outside the image dimensions.
- `keypoints`: Image coordinates and visibility in standard COCO format for each keypoint in the 17-point COCO skeleton. Visibility labels are defined as in `armature_keypoints`. Note that the hip keypoints in this data structure correspond to different locations than those in `armature_keypoints`. Specifically, they correspond to a more lateral location designed to better reflect where human annotators typically place the hips (e.g. in the COCO dataset).
- `num_keypoints`: Number of keypoints in the COCO skeleton with non-zero visibility.
- `quaternions`: 3D rotations for each degree-of-freedom in the SMPL-X model, relative to its parent in the kinematic tree, in wxyz order.

### Segmentation annotations

For each frame of a video, the following segmentation masks are provided:

- `image.{frame_number}.cseg.png`: Semantic segmentation.
- `image.{frame_number}.iseg.png`: Instance segmentation.
- `image.{frame_number}.iseg.{annotation_id}.png`: Instance segmentation without occlusion.


## Terms and Conditions
This work is licensed under a
[Creative Commons Attribution 4.0 International License](http://creativecommons.org/licenses/by/4.0/). Both academic and commercial applications are allowed.