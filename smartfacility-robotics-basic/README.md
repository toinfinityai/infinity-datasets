<p align="left">
  <img src="./assets/logo.png" width="30%">
</p>

# Smart Facility: Robotics Basic Dataset
An open-source synthetic vision dataset for smart facility and robotics applications.
<p align="center">
  <img src="./assets/robotics_teaser.gif" width="90%">
</p>

## Overview
The Smart Facility: Robotics Basic Dataset is a synthetic, open-source dataset for smart facility and robotics applications. It includes 20 videos as captured by a multi-camera system moving through a complex warehouse environment. The dataset features in-domain actions, items, and ground truth annotations that support a variety of algorithms, including:

- Human pose classification for smart ergonomics
- PPE classification and detection for safety/risk assessment
- Obstacle avoidance
- Inventory tracking
- Multi-camera SLAM applications

## Dataset Features
- 10 pairs of synchronized, multi-view videos, between 10 and 15 seconds in length 
- In-domain action sequences, including lifting and reaching movements with good and bad form
- Complex combinations of personal protective equipment, including hardhats, goggles, ear protection, and safety vests
- Fully segmented items on the floor and shelves
- Procedurally generated camera motion paths that are unique to each video and exhibit no object collisions
- Rich annotations, including fully segmented scenes and 3D keypoints

## Getting Started 
Download the dataset from the [Infinity Marketplace](https://marketplace.infinity.ai/products/smart-facility-robotics-basic-dataset), and use the provided [quickstart notebook](quickstart.ipynb) to visualize labels.

## Contents
For each dataset sample, we provide the following files:

- `{video_number}_params.json`: A JSON file describing the parameters used to generate the dataset sample. See [below](#dataset-parameters) for a description of each.
- `{video_number}_cam{camera_number}.mp4`: The RGB video for the specified camera. Cameras 0 and 1 follow the same path in 3D space, but are rotated 180 degrees with respect to one another. Camera 0 (`cam0`) moves forward along the generated path, while Camera 1 (`cam1`) moves backward along the path.
- `{video_number}_cam{camera_number}_labels.json`: A COCO-formatted JSON file of video-, frame-, and instance-level annotations and labels for the specified camera. See [below](#Annotations) for a full description of provided labels.
- `{video_number}_cam{camera_number}_segmentation.zip`: A zip file containing frame-level semantic and instance segmentation masks. For each avatar, instance segmentations masks are provided with and without occlusion.

## Dataset Parameters

The `{video_number}_params.json` file provided for each dataset sample includes the parameters that were used to generate that specific set of videos. For convenience, we provide a description of each parameter below:

- `scene`: Name of the 3D environment/scene.
- `num_people`: Number of people placed in the scene. In each video, 1 avatar walks through the scene, while all others perform an in-place action sequence.
- `frame_rate`: The frame rate of the rendered video.
- `random_seed`: The random seed used to generate the video.
- `num_cameras`: Number of synchronized cameras to simulate on the moving robot.
- `camera_type`: Type of camera (perspective or fisheye).
- `camera_focal_length`: Focal length of camera, in millimeters.
- `camera_field_of_view`: Field of view of fisheye lens, in degrees.
- `camera0_height`: Height of Camera 0, in meters.
- `camera0_yaw_deg`: Rotation of Camera 0 (relative to forward motion), in degrees.
- `camera1_height`: Height of Camera 1, in meters.
- `camera1_yaw_deg`: Rotation of Camera 1 (relative to forward motion), in degrees.
- `camera_pitch_deg`: Pitch of cameras (relative to ground plane), in degrees.
- `camera_min_path_length`: Minimum length of procedurally generated, closed-loop camera path.
- `image_width`: Width of rendered video(s), in pixels.
- `image_height`: Height of rendered video(s), in pixels.
- `enable_clutter`: Boolean flag indicating whether miscellaneous scene-related objects should be placed on the floor as obstacles.
- `min_dist_between_clutter_objects`: Minimum distance between objects placed on the floor, in meters.
- `video_duration`: The total duration of the rendered video, in seconds.
- `camera_speed`: Speed of the camera, in meters per second.

## Annotations

Each `{video_number}_cam{camera_number}_labels.json` file is provided in standard [COCO](https://cocodataset.org/#format-data) format. The third-party [pycocotools](https://pypi.org/project/pycocotools/) package provides useful utility functions for working with the data structure.

### Scene-level annotations
Scene-level annotations are provided for each video. They are accessible via the top-level `info` field of the JSON data structure. Scene-level annotations include:

- `camera_focal_len_mm`: Focal length of camera, in millimeters.
- `camera_sensor_height_mm`: Height of camera sensor, in millimeters.
- `camera_sensor_width_mm`: Width of camera sensor, in millimeters.
- `camera_K_matrix`: Intrinsic K matrix of the synthetic camera.

### Frame-level annotations
Frame-level annotations are provided for each frame of a video. They are accessible via the top-level `images` field of the JSON data structure. Frame-level annotations include:

- `frame_number`: The frame number of the corresponding image.
- `camera_location_m`: Location of the camera in the global coordinate system, in meters.
- `camera_P_matrix`: P matrix of the synthetic camera. This can be used to project any 3D position in the global coordinate system onto the image plane. Note that `P = K @ RT`.
- `camera_RT_matrix`: Extrinsic RT matrix of the synthetic camera (rotation + translation).

### Instance-level annotations
Instance-level annotations are provided for the each object in the image, including PPE items worn by the avatars and boxes on the shelves. Avatar-specific annotations are provided for every image, regardless of whether the avatar is in-frame or not. Instance-level annotations are accessible via the top-level `annotations` field of the JSON data structure. Instance-level annotations include:

- `color`: Normalized RGB value in the corresponding instance segmentation masks.
- `percent_in_fov`: Percentage of the vertices from the underlying mesh that are within the camera's field-of-view, regardless of whether those vertices are occluded by other objects. This value can be used to disambiguate whether sparse instance segmentation masks reflect a high degree of environmental occlusion versus the instance being out-of-frame.
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

- `person_idx`: Index of the person in the scene. Useful for collecting all annotations (across time) for a specific avatar.
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