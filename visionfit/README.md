<p align="left">
  <img src="../doc/logo.png" width="30%">
</p>

# Infinity VisionFit API

<p align="center">
  <img src="../doc/visionfit_teaser.gif" width="90%">
</p>

The VisionFit API generates synthetic data for applications at the intersection of computer **vision** and **fit**ness. The API allows users to generate videos of realistic avatars performing a variety of exercises. Our pixel-perfect labels  include everything from semantic segmentation masks and rep counts to 2D and 3D keypoints (with camera matrices!).

This folder contains the following tutorial notebooks:

- [large_job_demo.ipynb](large_job_demo.ipynb): This notebook shows how to submit many (100s) of jobs in a single batch, query their status in non-blocking mode, and compile the dataset's metadata into a dataframe for easy summarization/querying.

- [large_sweep_demo.ipynb](large_sweep_demo.ipynb): This notebook shows how to generate a large parameter sweep from a group of previews, query their status in non-blocking mode, and compile the dataset's metadata into a dataframe for easy summarization/querying.

- [workflow_demo.ipynb](workflow_demo.ipynb): This notebook demonstrates a common workflow when using our API for targeted scene creation: (1) generating a handful of diverse images in preview mode, (2) creating variants of a chosen preview by sweeping through a parameter of interest, and (3) generating a full video from one of the previous previews. The notebook also shows how to generate entirely random videos (without any previews) using only a few lines of code. 

- [explore_api_parameters.ipynb](explore_api_parameters.ipynb): This notebook showcases the flexibility of the VisionFit API. After generating a single video, we edit the same scene in fully controlled ways -- resulting in a series of otherwise identical videos with targeted aspects changed like avatar rotation, lighting conditions, number of reps, and even video resolution.

- [rep_counting_demo.ipynb](rep_counting_demo.ipynb): This notebook demonstrates how to use our synthetic data to bootstrap your deep learning models. We generate a batch of synthetic data, train an RNN-based rep counting model, and apply the model to a real world video.

## Parameter Descriptions

Below is the list of parameters you can use to control data generated with the VisionFit API:

- `scene`: Background environment/scene.
  - String that must be one of:
    - "GYM_1"
    - "BEDROOM_2"
    - "BEDROOM_4"
    - "BEDROOM_5"
- `exercise`: Name of exercise used in animation. See [below](#available-exercises) for a video of each.
  - String that must be one of:
    - "ARM_RAISE"
    - "BACK_SQUAT"
    - "BENT_OVER_TRICEP_KICKBACK_RIGHT"
    - "BICEP_CURL"
    - "BURPEE"
    - "CRUNCH"
    - "DEADLIFT"
    - "EXPLOSIVE_PUSH_UP"
    - "LEG_RAISE"
    - "PRESS_LEFT"
    - "PUSH_PRESS_LEFT"
    - "PUSH_UP"
    - "SIT_UP"
    - "SPLIT_SQUAT_RIGHT"
    - "SUPERMAN"
    - "UPPERCUT_LEFT"
    - "V_UP"
- `gender`: Avatar gender.
  - String that must be either "MALE" or "FEMALE"
- `num_reps`: Number of exercise repetitions in the returned video.
  - Integer that must be within the range of [1, 10].
- `seconds_per_rep`: Duration of baseline rep in seconds.
  - Floating point number that must be within the range of [1.0, 3.0] seconds.
- `max_rel_speed_change`: Maximum change in speed across reps in a single rep sequence, relative to the baseline speed; expressed as a fraction between 0 and 1.
  - Floating point number that must be within the range of [0.0, 1.0].
- `trim_start_frac`: Fraction of seed animation (from start to midpoint) to truncate at the start.
  - Floating point number that must be within the range of [0.0, 1.0].
  - Combined constraint: `trim_start_frac` + `trim_end_frac` < 0.9.
- `trim_end_frac`: Fraction of seed animation (from start to midpoint) to truncate at the end.
  - Floating point number that must be within the range of [0.0, 1.0].
  - Combined constraint: `trim_start_frac` + `trim_end_frac` < 0.9.
- `kinematic_noise_factor`: Scalar factor used to change the default kinematic noise added in generated animations.
  - Floating point number that must be within the range of [0.0, 2.0].
- `camera_height`: Height of viewing camera.
  - Floating point number that must be within the range of [0.1, 2.75] meters.
- `relative_camera_yaw_deg`: Camera yaw relative to avatar, in degrees, where 0 is directly facing the avatar.
  - Floating point number that must be within the range of [-45.0, 45.0] degrees.
- `relative_camera_pitch_deg`: Camera pitch relative to avatar, in degrees, where 0 is directly facing the avatar.
  - Floating point number that must be within the range of [-45.0, 45.0] degrees.
- `lighting_power`: Luminosity of the scene.
  - Floating point number that must be within the range of [0.0, 2000.0].
- `relative_avatar_angle_deg`: Relative avatar rotation, in degrees, where 0 is directly facing the camera.
  - Floating point number that must be within the range of [-180.0, 180.0].
- `frame_rate`: Output video frame rate.
  - Integer that must be one of {30, 24, 12, 8, 6}.
- `image_width`: Output image/video width in pixels.
  - Integer that must be within the range of [128, 512] pixels.
- `image_height`: Output image/video height in pixels.
  - Integer that must be within the range of [128, 512] pixels.

An example parameterization for the VisionFit API expressed as a Python `dict`:

```python
params_dict = {
    "scene": "BEDROOM_2",
    "exercise": "PUSH_UP",
    "gender": "FEMALE",
    "num_reps": 1,
    "seconds_per_rep": 1.0,
    "max_rel_speed_change": 0.0,
    "trim_start_frac": 0.0,
    "trim_end_frac": 0.0,
    "kinematic_noise_factor": 1.0,
    "camera_height": 0.75,
    "relative_camera_yaw_deg": 0.0,
    "relative_camera_pitch_deg": 0.0,
    "lighting_power": 100.0,
    "relative_avatar_angle_deg": 90.0,
    "frame_rate": 6,
    "image_width": 256,
    "image_height": 256,
}
```

## Available Exercises

<p align="center">
  <img src="../doc/visionfit_exercises.gif" width="100%">
</p>

## API Outputs
For each job, a zipped archive can be downloaded that includes the following files:

- `job.json`: A JSON file describing the original parameters that generated the job.
- `video_preview.png`: A single image file, corresponding to the first frame in the rendered video.
- `video.rgb.mp4`: The resulting RGB video.
- `video.rbg.json`: A COCO-formatted json file of video-, frame-, and instance-level annotations and labels. See [below](#Annotations) for a full description of provided labels.
- `video.rgb.zip`: A zipped file containing frame-level semantic and instance segmentation masks (with and without occlusion). 

## Annotations

The `video.rgb.json` file is provided in standard [COCO](https://cocodataset.org/#format-data) format. The third-party [pycocotools](https://pypi.org/project/pycocotools/) package provides useful utility functions for working with the data structure.

### Scene-level annotations

Scene-level annotations are provided for each video. They are accessible via the top-level `info` field of the `video.rbg.json` data structure. Scene-level annotations include:


* `camera_pitch`: Pitch rotation of the camera in the global coordinate system, in degrees. A value of 90 indicates the camera's line of sight is parallel to the ground plane.
* `camera_yaw`: Yaw rotation of the camera in the global coordinate system, in degrees. A value of 0 indicates the camera's line of sight is aligned with the +Y axis.
* `camera_location`: Location of the camera in the global coordinate system, in meters.
* `camera_height`: Height of the camera in the global coordinate system, in meters.
* `avatar_yaw`: Yaw rotation of the avatar in the global coordinate system, in degrees.
* `avatar_presenting_gender`: Gender of the underlying SMPL-X body model.
* `avatar_attire_top`/`avatar_attire_bottom`: Clothing type used in the applied UV texture.
* `avatar_betas`: 10 shape coefficients for the underlying SMPL-X body model.
* `avatar_waist_circumference`: Circumference of the SMPL-X body model's waist, in meters.
* `avatar_location`: Location of the avatar in the global coordinate system, in meters.
* `camera_P_matrix`: P matrix of the synthetic camera. This can be used to project any 3D position in the global coordinate system onto the image plane. Note that `P = K @ RT`.
* `camera_K_matrix`: Intrinsic K matrix of the synthetic camera.
* `camera_RT_matrix`: Extrinsic RT matrix of the synthetic camera (rotation + translation).

### Frame-level annotations
Frame-level annotations are provided for each frame of a video. They are accessible via the top-level `images` field of the `video.rbg.json` data structure. Frame-level annotations include:

- `frame_number`: The frame number of the corresponding image.
- `rep_count_from_start`: The number of repetitions completed since the beginning of the video PLUS a float in the range of [0,1] that indicates the current frame’s relative position in the exercise sequence. For example, a value of 4.23 indicates that 4 full repetitions have been completed since the beginning of the video, and that the current frame corresponds to 23% completion of the next one.
- `rep_count_from_intermediate`: This value is conceptually similar to `rep_count_from_start`, but is instead indexed to the midpoint of the rep. We provide both since users may wish to define (for example) the point of most flexion OR the point of most extension as the rep inflection point.

### Instance-level annotations
Instance-level annotations are provided for every unique object segmented in an image. This includes the avatar, as well as any other objects of interest that are present, such as dumbbells. Instance-level annotations are accessible via the top-level `annotations` field of the `video.rbg.json` data structure.


* `color`: Normalized RGB value in the corresponding instance segmentation masks
* `percent_in_fov`: Percentage of the vertices from the underlying mesh that are within the camera's field-of-view, regardless of occlusion status. This value can be used to disambiguate whether sparse instance segmentation masks reflect a high degree of environmental occlusion versus the instance being out-of-frame.
* `percent_occlusion`: Percentage of the instance that is not visibile due to environmental occlusion (i.e. objects in the foreground). It is quantified as the relative difference between the occluded and unoccluded instance segmentation masks, which are also provided.
* `bbox`: Bounding box in standard COCO format
* `segmentation`: Polygon segmentation in standard COCO format
* `area`: Area enclosed by polygon segmentation
* `cuboid_coordinates`: Image coordinates of the surroinding 3D cuboid, with axes that are parallel to the global coordinate system. The order of the cuboid points is shown below.

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

* `armature_keypoints`: A data structure including image coordinates (x,y), visibility (v), depth from camera (z, in meters), and 3D position in the global coordinate system (x_global, y_global, z_global; in meters) for each degree-of-freedom in the underlying SMPL-X model. Visibility values indicate whether keypoints are not in the image frame (0), in the image frame but occluded (1), or visible (2).
* `vertex_keypoints`: A data structure including image coordinates (x,y), visibility (v), depth from camera (z, in meters), and 3D position in the global coordinate system (x_global, y_global, z_global; in meters) for various anatomical points of interest on the SMPL-X body mesh. Points of interest include the ears and nose. Visibility labels are defined as in `armature_keypoints`.
* `keypoints`: Image coordinates and visibility in standard COCO format for each keypoint in the 17-point COCO skeleton. Visibility labels are defined as in `armature_keypoints`. Note that the hip keypoints in this data structure correspond to different locations than those in `armature_keypoints`. Specifically, they correspond to a more lateral location designed to better reflect where human annotators typically place the hips (e.g. in the COCO dataset).
* `num_keypoints`: Number of keypoints in the COCO skeleton with non-zero visibility.
* `quaternions`: 3D rotations for each degree-of-freedom in the SMPL-X model, relative to its parent in the kinematic tree, in wxyz order.

### Segmentation annotatations

For each frame of a video, the following segmentation masks are provided:

* `image.{frame_number}.cseg.png`: Semantic segmentation
* `image.{frame_number}.iseg.png`: Instance segmentation
* `image.{frame_number}.iseg.{annotation_id}.png`: Instance segmentation without occlusion
