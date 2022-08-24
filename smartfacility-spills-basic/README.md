<p align="left">
  <img src="./assets/logo.png" width="30%">
</p>

# Smart Facility: Spills Basic Dataset
An open-source synthetic vision dataset for safety applications and spill detection.

<p align="center">
  <img src="./assets/spills_teaser.gif" width="90%">
</p>

## Overview
The Smart Facility: Spills Basic dataset is a synthetic, open-source dataset for safety applications and spill detection. It features 150 videos of photorealistic liquid spills across 15 common settings. Spills take on in-context reflections, caustics, and depth based on the surrounding environment, lighting, and floor. Each video contains a spill of unique properties (size, color, profile, and more) and is accompanied by pixel-perfect labels and annotations. This dataset can be used to develop computer vision algorithms to detect the location and type of spill from the perspective of a fixed camera.

## Dataset Features
+ 150 videos
+ 4 environments where spills commonly occur
+ 15 unique indoor scenes
+ Realistic spill appearance
+ Diverse variation in spill geometry
+ Multiple spill colors
+ Rich annotations

## Getting Started 
Download the dataset from the [Infinity Marketplace](https://marketplace.infinity.ai/products/smart-facility-spills-basic-dataset), and use the provided [quickstart notebook](quickstart.ipynb) to visualize labels.

## Contents
For each dataset sample, we provide the following files:

- `{video_number}_params.json`: A JSON file describing the parameters used to generate the dataset sample. See [below](#dataset-parameters) for a description of each.
- `{video_number}.mp4`: The RGB video of the spill.
- `{video_number}_labels.json`: A COCO-formatted JSON file of frame- and instance-level annotations and labels. See [below](#Annotations) for a full description of provided labels.
- `{video_number}_segmentation.zip`: A zipped file containing frame-level semantic and instance segmentation masks. 

## Dataset Parameters

The `{video_number}_params.json` file provided for each dataset sample includes the parameters that were used to generate that specific video. For convenience, we provide a description of each parameter below:

- `scene`: Background environment/scene.
- `color`: The color of the spill in the rendered RGB image.
- `size`: The relative size of the spill at the end of the animation, in arbitrary units. Higher values correspond to a larger overall spill.
- `aspect_ratio`: The aspect ratio of the rendered spill. A value of 1.0 corresponds to a more circular/symmetric spill shape.
- `profile_irregulatity`: The irregularity of the spills perimeter. A value of 0.0 results in a very smooth outer perimeter. A value of 1.0 results in a highly irregular spill perimeter.
- `depth`: The relative depth of the spill, in arbitrary units.
- `frame_rate`: The frame rate of the rendered video.
- `video_duration`: The total duration of the rendered video, in seconds.
- `random_seed`: The random seed used to generate the video.

## Annotations

The `{video_number}_labels.json` file is provided in standard [COCO](https://cocodataset.org/#format-data) format. The third-party [pycocotools](https://pypi.org/project/pycocotools/) package provides useful utility functions for working with the data structure.

### Frame-level annotations
Frame-level annotations are provided for each frame of a video. They are accessible via the top-level `images` field of the JSON data structure. Frame-level annotations include:

- `frame_number`: The frame number of the corresponding image.
- `spill_visible`: A boolean flag indicating whether the frame's segmentation mask includes any pixels associated with the spill.

### Instance-level annotations
Instance-level annotations are provided for the floor and spill in each frame. Instance-level annotations are accessible via the top-level `annotations` field of the JSON data structure. 

**Note: The instance-level annotations enumerated below are only provided when a non-empty bounding box can be defined.** Annotations may not be provided for the spill in early frames when it occupies an area less than 3 pixels in height and width.

* `color`: Normalized RGB value in the corresponding instance segmentation masks.
* `bbox`: Bounding box in standard COCO format.
* `segmentation`: Polygon segmentation in standard COCO format.
* `area`: Area enclosed by polygon segmentation.

### Segmentation annotatations

For each frame of a video, the following segmentation masks are provided:

* `image.{frame_number}.cseg.png`: Semantic segmentation.
* `image.{frame_number}.iseg.png`: Instance segmentation.

## Terms and Conditions
This work is licensed under a
[Creative Commons Attribution 4.0 International License](http://creativecommons.org/licenses/by/4.0/). Both academic and commercial applications are allowed.