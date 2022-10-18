<p align="left">
  <img src="./assets/logo.png" width="30%">
</p>

# Fitness Low-Light Dataset
A synthetic video dataset for fitness and physical therapy applications featuring a variety of challenging lighting scenarios.

<p align="left">
  <img src="./assets/fitness_lowlight_teaser.gif">
</p>

## Overview 
The Fitness Low-Light Dataset focuses on various challenging lighting scenarios within the home fitness and physical therapy context. The dataset includes videos of 3 different exercises being performed by diverse avatars in settings with low light and correspondingly low signal to noise ratio (SNR). The dataset introduces several output metrics that report brightness, contrast, and SNR associated with each frame.

## Dataset Features
- 120 videos spanning 3 exercises.
- Varying low-light scene illumination.
- Outputs chosen to challenge state-of-the-art keypoint detection models.
- Within the low-light illumination context, variety of brightnesses and color contrasts.
- Illumination-dependent noise simulation.
- No out-of-frame or occluded avatars.
- 7-15 reps per video.
- 30 FPS, 512x512 resolution videos.
- Avatars with diverse heights and body shapes.
- Variation in kinematic trajectories and exercise speeds.
- 4 interior environments.


## Exercises

The dataset includes the following exercises: 

1. Bicep Curls (alternating)
2. Pushups
3. Sumo Squats

## Getting Started

Download the dataset from the [Infinity Marketplace](https://marketplace.infinity.ai/products/fitness-low-light-dataset), and use the provided [quickstart notebook](quickstart.ipynb) to visualize labels and filter the dataset based on provided metadata.

**NOTE:** If you want access to this dataset for FREE, send an email to info@toinfinity.ai 

## Contents
For each dataset sample, we provide the following files:

- `{video_number}_params.json`: A JSON file describing the parameters used to generate the dataset sample. See [below](#dataset-parameters) for a description of each.
- `{video_number}.mp4`: The rendered RGB video.
- `{video_number}_labels.json`: A COCO-formatted json file of video-, frame-, and instance-level annotations and labels. See [below](#Annotations) for a full description of provided labels.
- `{video_number}_segmentation.zip`: A zipped file containing frame-level semantic and instance segmentation masks. 

## Dataset Parameters
The `{video_number}_params.json` file provided for each dataset sample includes the parameters that were used to generate that specific video. For convenience, we provide a description of each parameter below:

- `scene`: Background environment/scene.
- `exercise`: Name of exercise used in the animation. Reference videos and metadata for each exercise are available [here](https://docs.google.com/spreadsheets/d/15Ofjc0dA6IDihMzQguEiB0KxwjWSnMw1moIJglRyTCk/edit#gid=0).
- `gender`: Avatar gender.
- `num_reps`: Number of exercise repetitions in the returned video.
- `rel_baseline_speed`: Baseline speed of animation, relative to default (natural) speed.
- `max_rel_speed_change`: Maximum change in speed across reps in a single rep sequence, relative to the baseline speed.
- `trim_start_frac`: Fraction of seed animation (from start to midpoint) to truncate at the start.
- `trim_end_frac`: Fraction of seed animation (from start to midpoint) to truncate at the end.
- `kinematic_noise_factor`: Scaling factor used to adjust the amount of kinematic noise added in the simulated movement.
- `camera_distance`: Approximate distance between the camera and avatar, in meters.
- `camera_height`: Height of the viewing camera, in meters.
- `avatar_identity`: Integer-based unique idenfier that controls the chosen avatar appearance. Reference images for each identity are provided [here](https://drive.google.com/drive/folders/1HsCLh0YM_z4fM594yVQ_UKMh8zzzReyX).
- `relative_height`: Relative height of the avatar. Positive values result in a taller avatar. This value corresponds to the first PCA coefficient in the SMPL-X model's beta parameter.
- `relative_weight`: Relative weight of the avatar. Positive values result in an avatar with greater weight. This value corresponds to the second PCA coefficient in the SMPL-X model's beta parameter.
- `relative_camera_yaw_deg`: Camera yaw relative to avatar, in degrees, where 0 is directly facing the avatar.
- `relative_camera_pitch_deg`: Camera pitch relative to avatar, in degrees, where 0 is directly facing the avatar.
- `lighting_power`: Luminosity of the scene.
- `relative_avatar_angle_deg`: Relative avatar rotation, in degrees, where 0 is directly facing the camera.
- `frame_rate`: Output video frame rate.
- `image_width`: Output image/video width in pixels.
- `image_height`: Output image/video height in pixels.

## Annotations

The `{video_number}_labels.json` file is provided in standard [COCO](https://cocodataset.org/#format-data) format. The third-party [pycocotools](https://pypi.org/project/pycocotools/) package provides useful utility functions for working with the data structure.

### Scene-level annotations

Scene-level annotations are provided for each video. They are accessible via the top-level `info` field of the JSON data structure. Scene-level annotations include:


- `camera_pitch`: Pitch rotation of the camera in the global coordinate system, in degrees. A value of 90 indicates the camera's line of sight is parallel to the ground plane.
- `camera_yaw`: Yaw rotation of the camera in the global coordinate system, in degrees. A value of 0 indicates the camera's line of sight is aligned with the +Y axis.
- `camera_location`: Location of the camera in the global coordinate system, in meters.
- `camera_height`: Height of the camera in the global coordinate system, in meters.
- `avatar_yaw`: Yaw rotation of the avatar in the global coordinate system, in degrees.
- `avatar_presenting_gender`: Gender of the underlying SMPL-X body model.
- `avatar_attire_top`/`avatar_attire_bottom`: Clothing type used in the applied UV texture.
- `avatar_betas`: 10 shape coefficients for the underlying SMPL-X body model.
- `avatar_waist_circumference`: Circumference of the SMPL-X body model's waist, in meters.
- `avatar_location`: Location of the avatar in the global coordinate system, in meters.
- `avatar_identity`: Integer-based unique idenfier that controls the chosen avatar appearance.
- `camera_P_matrix`: P matrix of the synthetic camera. This can be used to project any 3D position in the global coordinate system onto the image plane. Note that `P = K @ RT`.
- `camera_K_matrix`: Intrinsic K matrix of the synthetic camera.
- `camera_RT_matrix`: Extrinsic RT matrix of the synthetic camera (rotation + translation).

### Frame-level annotations
Frame-level annotations are provided for each frame of a video. They are accessible via the top-level `images` field of the JSON data structure. Frame-level annotations include:

- `frame_number`: The frame number of the corresponding image.
- `rep_count_from_start`: The number of repetitions completed since the beginning of the video PLUS a float in the range of [0,1] that indicates the current frame’s relative position in the exercise sequence. For example, a value of 4.23 indicates that 4 full repetitions have been completed since the beginning of the video, and that the current frame corresponds to 23% completion of the next one.
- `rep_count_from_intermediate`: This value is conceptually similar to `rep_count_from_start`, but is instead indexed to the midpoint of the rep. We provide both since users may wish to define (for example) the point of most flexion OR the point of most extension as the rep inflection point.
- `mean_image_brightness`: The mean perceptual lightness, L\*, in the [CIELAB color space](https://en.wikipedia.org/wiki/CIELAB_color_space). L\* for a given pixel varies from 0 to 100, where 100 is the “lightest”. L\* is approximately linear to human perception.
- `mean_avatar_brightness`; The mean perceptual lightness, L\*, of the avatar pixels.
- `mean_avatar_halo_brightness`: The mean perceptual lightness, L\*, of a ~20-pixel halo surrounding the avatar region.
- `snr`: the signal to noise ratio, averaged over all pixels, due to the simulated [photon shot noise](https://en.wikipedia.org/wiki/Shot_noise).
- `mean_brightness_contrast`: The magnitude of difference between the L\* brightness values inside and outside the avatar. This is effectively a grayscale contrast. Note that the contrast is computed as the average contrast over several sectors of the avatar and its surroundings.
- `mean_color_contrast`: This is the [Delta E (dE00)](http://zschuessler.github.io/DeltaE/learn/) metric, a kind of distance metric in the CIELAB color space, which varies from 0 to 100. On this scale, a color contrast near zero means colors are imperceptibly different, while colors above a contrast of 50 are more different than they are similar. Note that the contrast is computed as the average contrast over several sectors of the avatar and its surroundings.

### Instance-level annotations
Instance-level annotations are provided for every unique object segmented in an image. This includes the avatar, dumbbells (if present), and the miscellaneous objects used to introduce complex occlusion patterns. Instance-level annotations are accessible via the top-level `annotations` field of the JSON data structure. They include:


- `color`: Normalized RGB value in the corresponding instance segmentation masks
- `percent_in_fov`: Percentage of the vertices from the underlying mesh that are within the camera's field-of-view, regardless of whether those vertices are occluded by other objects. This value can be used to disambiguate whether sparse instance segmentation masks reflect a high degree of environmental occlusion versus the instance being out-of-frame.
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

- `armature_keypoints`: A data structure including image coordinates (x,y), visibility (v), depth from camera (z, in meters), and 3D position in the global coordinate system (x_global, y_global, z_global; in meters) for each degree-of-freedom in the underlying SMPL-X model. Visibility values indicate whether keypoints are not in the image frame (0), in the image frame but occluded (1), or visible (2).
- `vertex_keypoints`: A data structure including image coordinates (x,y), visibility (v), depth from camera (z, in meters), and 3D position in the global coordinate system (x_global, y_global, z_global; in meters) for various anatomical points of interest on the SMPL-X body mesh. Points of interest include the ears and nose. Visibility labels are defined as in `armature_keypoints`.
- `keypoints`: Image coordinates and visibility in standard COCO format for each keypoint in the 17-point COCO skeleton. Visibility labels are defined as in `armature_keypoints`. Note that the hip keypoints in this data structure correspond to different locations than those in `armature_keypoints`. Specifically, they correspond to a more lateral location designed to better reflect where human annotators typically place the hips (e.g. in the COCO dataset).
- `num_keypoints`: Number of keypoints in the COCO skeleton with non-zero visibility.
- `quaternions`: 3D rotations for each degree-of-freedom in the SMPL-X model, relative to its parent in the kinematic tree, in wxyz order.

### Segmentation annotations

For each frame of a video, the following segmentation masks are provided:

- `image.{frame_number}.cseg.png`: Semantic segmentation
- `image.{frame_number}.iseg.png`: Instance segmentation
- `image.{frame_number}.iseg.{annotation_id}.png`: Instance segmentation without occlusion

## Terms and Conditions
This dataset is licensed under Infinity AI’s [Terms and Conditions](https://infinity.ai/terms). 
