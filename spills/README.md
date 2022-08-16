<p align="left">
  <img src="../doc/logo.png" width="30%">
</p>

# Infinity Spills API [0.1.0]

<p align="center">
  <img src="../doc/spills_teaser.gif" width="90%">
</p>

The Spills API generates photorealistic videos of spills in various retail, industrial, and workplace environments. The API allows users to generate videos of spills with various configurations like shape, size, and color. Our pixel-perfect labels include standard object detection and localization annotations for the spill and floor, including segmentation masks and bounding boxes.

This folder contains the following tutorial notebooks:

- [explore_api_parameters.ipynb](explore_api_parameters.ipynb): This notebook showcases the flexibility of the Spills API. After generating a single video, we edit the same scene in fully controlled ways -- resulting in a series of otherwise identical videos with targeted aspects changed like spill size, color, and viscosity.

- [large_job_demo.ipynb](large_job_demo.ipynb): This notebook shows how to submit many (100s) of jobs in a single batch, query their status in non-blocking mode, and compile the dataset's metadata into a dataframe for easy summarization/querying.

## Parameter Descriptions

For convenience, we provide the parameters of the Spills API along with their descriptions below. The `display_parameters()` function can also be used to display the allowable values and default values for each parameter, e.g.:

```
from infinity_tools.spills import api
from infinity_tools.common.vis.notebook import display_parameters
display_parameters(api, token="YOUR_TOKEN_HERE")
```

- `scene`: Background environment/scene.
- `color`: The color of the spill in the rendered RGB image.
- `size`: The relative size of the spill at the end of the animation, in arbitrary units. Higher values result in a larger overall spill.
- `aspect_ratio`: The aspect ratio of the rendered spill. A value of 1.0 corresponds to a more circular/symmetric spill shape.
- `profile_irregulatity`: The irregularity of the spills perimeter. A value of 0.0 results in a very smooth outer perimeter. A value of 1.0 results in a highly irregular spill perimeter.
- `depth`: The relative depth of the spill, in arbitrary units.
- `frame_rate`: The frame rate of the rendered video.
- `video_duration`: The total duration of the rendered video, in seconds.
- `random_seed`: A random integer seed that can be used for reproducibility and parametric sweeps.

An example parameterization for the Spills API expressed as a Python `dict`:

```python
params_dict = {
	"scene": "WAREHOUSE_0002",
	"color": "TRANSPARENT",
	"size": 62.54,
	"aspect_ratio": 1.69,
	"profile_irregularity": 0.42,
	"depth": 1.41,
	"frame_rate": 24,
	"video_duration": 6.37,
	"random_seed": 1916477150,
}
```

## API Outputs
For each job, a zipped archive can be downloaded that includes the following files:

- `job.json`: A JSON file describing the original parameters that generated the job.
- `video_preview.png`: A single image file, corresponding to the last frame in the rendered video.
- `video.rgb.mp4`: The resulting RGB video.
- `video.rbg.json`: A COCO-formatted json file of video-, frame-, and instance-level annotations and labels. See [below](#Annotations) for a full description of provided labels.
- `video.rgb.zip`: A zipped file containing frame-level semantic and instance segmentation masks. 

## Annotations

The `video.rgb.json` file is provided in standard [COCO](https://cocodataset.org/#format-data) format. The third-party [pycocotools](https://pypi.org/project/pycocotools/) package provides useful utility functions for working with the data structure.

### Frame-level annotations
Frame-level annotations are provided for each frame of a video. They are accessible via the top-level `images` field of the `video.rbg.json` data structure. Frame-level annotations include:

- `frame_number`: The frame number of the corresponding image.
- `spill_visible`: A boolean flag indicating whether the frame's segmentation mask includes any pixels associated with the spill.

### Instance-level annotations
Instance-level annotations are provided for the floor and spill in each frame. Instance-level annotations are accessible via the top-level `annotations` field of the `video.rbg.json` data structure. 

**Note: The instance-level annotations enumerated below are only provided when a non-empty bounding box can be defined.** Annotations may not be provided for the spill in early frames when it occupies an area less than 3 pixels in height and width.

* `color`: Normalized RGB value in the corresponding instance segmentation masks
* `bbox`: Bounding box in standard COCO format
* `segmentation`: Polygon segmentation in standard COCO format
* `area`: Area enclosed by polygon segmentation

### Segmentation annotatations

For each frame of a video, the following segmentation masks are provided:

* `image.{frame_number}.cseg.png`: Semantic segmentation
* `image.{frame_number}.iseg.png`: Instance segmentation
