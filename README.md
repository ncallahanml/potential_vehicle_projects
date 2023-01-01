# Parking Lot Detection
Yolov5/7 Parking Lot Vehicle Detection

# Table of Contents:
1. [`Objective`](#objective)
2. [`Detection`](#detection-methods)
3. [`Tracking`](#tracking)
4. [`Clustering`](#clustering)
5. [`Feature Extraction`](#feature-extraction)
6. [`Production`](#production)
7. [`References`](#references)
8. [`Resources`](#resources)

## Objective

Measure the occupancy of a parking lot by learning the number of available parking spaces as well as detecting when vehicles occupy them.

## Detection Methods

The latest YOLO models are available for both object detection and instance segmentation. Many options exist to balance performance and accuracy, detection should be quite accurate out of the box during the daytime. 

### Model Versions:
YOLOv7 is almost universally better than YOLOv5 in terms of both performance and accuracy, though currently YOLOv5 is easier to handle in the code.
- `5` - YOLOv5 generation of models
- `7` - YOLOv7 generation of models

### Model Sizes:
Larger model sizes mean better accuracy, but slower processing.
- ` `    - Default model, smallest available size
- `x`    - Increased size of default model
- `-e6`  - Largest available model
- `-seg` - Midsized segmentation model

### Training Sets:

The default YOLOv5 and YOLOv7 are pretrained on the COCO dataset, which does a great job of detecting vehicles. However, to better deal with parking lots in particular more options are added. To accomodate detection of empty parking spaces, two aggregate datasets were assembled from existing parking datasets and used to fine tune the COCO detections. Performance should not be impacted by dataset selection, but COCO offers more flexibility.

- `coco` -  Standard object detection & instance segmentation set for large scale pretraining including vehicles & people
  - [`COCO2017`](https://cocodataset.org/#download)
- `pklt` - Vehicle & Parking space object detection set for a camera position angled slightly down towards the lot
  - [`PKLotV0`](https://universe.roboflow.com/deep-learning-e9tul/dataset-format-conversion-zcqpw/dataset/2/download/yolov7pytorch)
  - [`PKLotV1`](https://universe.roboflow.com/brad-dwyer/pklot-1tros)
  - [`PKLotV2`](https://universe.roboflow.com/zoja-scekic-nt9oo/pklotv2) 
- `aerl` - Vehicle & Parking space object detection from an aerial viewpoint
  - [`AerialV0`](https://universe.roboflow.com/deep-learning-e9tul/dataset-format-conversion-zcqpw)
  - [`AerialV1`](https://universe.roboflow.com/fastnuces/parking-hnrcc)
  - [`AerialV2`](https://universe.roboflow.com/ee541/parking-lot-fmzu9)
  
### Weight Naming Scheme:

yolov **$MODELVERSION** **$MODELSIZE** _ **$TRAININGSET**.pt

### Examples:

*yolov7-e6_coco.pt*  
*yolov5-seg_aerl.pt*

## Tracking

Basic tracking is necessary to measure the amount of time vehicles are stopped, as including moving vehicles as in the parking space clustering could quickly interfere with appropriate results. However, movement tracking is not required except in these few extensions where tracking of moving vehicles may be useful.

### Options:

- `stopped` - Track stopped vehicles 
- `nearby`  - Track vehicles by metrics independent of travel direction
- `norfair` - Track vehicles with [Norfair](https://github.com/tryolabs/norfair) tracker, requires framerate > 5fps

### Extensions:
1. `Stopped (Not Parked) Vehicles` - Distinguishing stopping at lights, traffic jams, stop signs, etc from longer term parking is necessary to limit spurious inclusions in the clustering algorithm. Tracking of vehicle paths should allow more detailed elimination of temporarily stopped vehicles.
2. `Traffic Patterns` - Much can be gleaned from time series analysis of the population of parked vehicles, but understanding the kinematics of moving vehicles within the scene requires a sense of direction, which requires at least a basic tracking algorithm.
3. `Tracking Pedestrians` - It is simple to add humans to the available detection classes, checking movement of pedestrians to gather data about interactions with cars or busy sections of the scene would be done best with a more reliable tracking mechanism.

## Clustering

To learn the locations of available parking spaces, a clustering algorithm is used to conglomerate points in the scene detected by the YOLO models. Difficulty in model tuning is in the ability to properly clean YOLO outputs so that the clustering algorithm will not make spurious parking spot 'detections' that would bias the metrics for occupancy.

### Algorithms:
Differing shapes of clusters caused by uncertainty in center estimation or other environment aspects of a scene can result in better or worse performance for each clustering algorithm shown below. Aside from shape of clusters, speed, handling of outliers, and interpretability are relevant factors when choosing an appropriate algorithm. Ideally the data cleaning prior to clustering will make the clustering algorithm's job easy, so that all algorithms return similar results. All clustering algorithms implemented here come in the [Sklearn](https://scikit-learn.org/stable/modules/clustering.html) format, so many drop in alternatives exist.

- [`KMeans`](https://scikit-learn.org/stable/modules/generated/sklearn.cluster.MiniBatchKMeans.html#sklearn.cluster.MiniBatchKMeans) - Mean separation distance clustering for clusters with even distributions away from the center.
- [`Spectral`](https://scikit-learn.org/stable/modules/generated/sklearn.cluster.SpectralClustering.html#sklearn.cluster.SpectralClustering) - Improved clustering of nested clusters compared to KMeans. Slightly slower than KMeans.
- [`HDBSCAN`](https://hdbscan.readthedocs.io/en/latest/how_hdbscan_works.html) - DBSCAN variant for fast clustering, especially of non circular clusters with outliers present. Typically the fastest available algorithm.
- [`OPTICS`](https://scikit-learn.org/stable/modules/generated/sklearn.cluster.OPTICS.html) - Similar to DBSCAN, generally more accurate at a significant computational cost. Works well with large datasets.

## Feature Extraction:

Available metrics to draw from the run, will be updated in real time or summarized at the end of the run dependent on settings. Out of the box very little data aside from the relevant occupancy information is collected, however there is plenty of room for expansion to getting detailed scene analysis. See [Tracking](#tracking) section for relevant ideas.

### Parked Vehicle Counts:

Number of parked vehicles. The total vehicle count is also available, but the measures are very different when cars are moving through the parking lot or a street is included in frame that will include moving vehicles.

### Total Available Spaces:

Estimated number and location of parking spots. This is used to estimate occupancy with the above vehicle counts.

### Parking Durations:

The distribution of durations in the parking lot, how long vehicles tend to stay parked. The accuracy is increased when the framerate is higher, as infrequent frame captures could miss one car leaving a parking lot to be replaced by another.

## Production

When running this project full time as intended, certain options should be used to match the overall availability and needs. For applications that are intended to return detailed but not necessarily immediate results, slower settings can be used to increase accuracy and print out internal data. For applications where occupancy rates should be accurate in real time, some costs can be cut that still allow reasonable accuracy. 

### Output Options:

- `silent` - print the relevant occupancy statistics and nothing else. ***Overrides other output options***
- `plot` - Print out plots for the relevant metrics.
- `annotate` - Labels & saves images that are run through YOLO. Can significantly slow operation.
- `save` - Stores tabular results as CSVs
- `debug` - default, prints extensive debugging statements
 
### Classes:

Which classes to label in images. Currently this doesn't affect numeric results of the run, only useful for labeling image outputs. Classes can be labeled either as the number associated with their class, or their associated label name. Available classes depend on the model in use.

| [*Dataset*](#training-sets)  | [*Classes*](#class-numbers-&-labels)   | *Defaults*             |
|:-------------:|:------------:|:----------------------:|
| `COCO`        | [Classes](#coco)  | Cars, Trucks           |
| `Aerial`      | [Classes](#aerial)  | Vehicles, Empty Spaces |
| `Parking Lot` | [Classes](#pklot)  | Vehicles, Empty Spaces |

### Framerate:

How frequently an image is captured and processed by the algorithm. More FPS increases accuracy at the cost of added computation.

- `min` - Single shot every 20 seconds, only tracks stopped vehicles
- `base` - 2 FPS, Faster acquisition of stopped vehicles, only allows basic tracking of slow vehicles
- `fast` - 10 FPS, Computationally more expensive, faster acquisition of stopped vehicles, better tracking
- `max` - Dependent on system specifications, process new image as soon as previous determination is made
- `custom` - Custom framerate, completely up to the user but may cause significant inaccuracy. Numbers larger than max framerate will reduce to the max framerate

### Reset:

Necessary scheduled restarts for the system to attempt to avoid unexpected crashes and clean up the environment as necessary.

#### Frequency:

- `hourly` - reset every hour, useful if memory issues cause crashes after running for less than 24 hours
- `daily` - default, resets every night
- `weekly` - maximum duration between resets

#### Scope:

- `soft` - resets algorithm only
- `hard` - default, reset entire device if available

#### Wipe:

- `all` - clear all results during reset
- `runtime` - clear runtime results but not summary printouts
- `summary` - clear summary printouts but not runtime results

### Finish:

- `video` - compiles resulting images into a video
- `plot` - plots gathered metrics over the course of the run
- `metrics` - outputs CSV containing time series information for the run

## References


## Resources
### Model Weights Download:
| COCO | Aerial | Parking Lot | Objective | Size |
|:----:|:------:|:-----------:|:---------:|:----:|
|[yolov5m_coco.pt]()    | [yolov5m_aerl.pt]()    | [yolov5m_pklt.pt]()    | **Object Detection**      | `Medium`      |
|[yolov7_coco.pt]()     | [yolov7_aerl.pt]()     | [yolov7_pklt.pt]()     | **Object Detection**      | `Medium`      |
|[yolov7x_coco.pt]()    | [yolov7x_aerl.pt]()    | [yolov7x_pklt.pt]()    | **Object Detection**      | `Large`       |
|[yolov5x_coco.pt]()    | [yolov5x_aerl.pt]()    | [yolov5x_pklt.pt]()    | **Object Detection**      | `Large`       |
|[yolov5-l6_coco.pt]()  | [yolov5-l6_aerl.pt]()  | [yolov5-l6_pklt.pt]()  | **Object Detection**      | `Extra Large` |
|[yolov7-e6_coco.pt]()  | [yolov7-e6_aerl.pt]()  | [yolov7-e6_pklt.pt]()  | **Object Detection**      | `Extra Large` |
|[yolov5-seg_coco.pt]() | [yolov5-seg_aerl.pt]() | [yolov5-seg_pklt.pt]() | **Instance Segmentation** | `Large`       |
|[yolov7-seg_coco.pt]() | [yolov7-seg_aerl.pt]() | [yolov7-seg_pklt.pt]() | **Instance Segmentation** | `Large`       |


### Class Numbers & Labels:

#### COCO
| Class Num | Class Label | Class Num | Class Label | Class Num | Class Label | Class Num | Class Label |
|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
|1| person |21| elephant |41| wine glass |61| dining table |
|2| bicycle |22| bear |42| cup |62| toilet |
|3| car |23| zebra |43| fork |63| tv |
|4| motorcycle |24| giraffe |44| knife |64| laptop |
|5| airplane |25| backpack |45| spoon |65| mouse |
|6| bus |26| umbrella |46| bowl |66| remote |
|7| train |27| handbag |47| banana |67| keyboard |
|8| truck |28| tie |48| apple |68| cell phone |
|9| boat |29| suitcase |49| sandwich |69| microwave |
|10| traffic light |30| frisbee |50| orange |70| oven |
|11| fire hydrant|31| skis |51| broccoli |71| toaster |
|12| stop sign|32| snowboard |52| carrot |72| sink |
|13| parking meter|33| sports ball |53| hot dog |73| refrigerator |
|14| bench|34| kite |54| pizza |74| book |
|15| bird|35| baseball bat |55| donut |75| clock |
|16| cat|36| baseball glove |56| cake |76| vase |
|17| dog|37| skateboard |57| chair |77| scissors |
|18| horse |38| surfboard |58| couch |78| teddy bear |
|19| sheep |39| tennis racket |59| potted plant |79| hair drier |
|20| cow |40| bottle |60| bed |80| toothbrush |

#### PKLot
| Class Num | Class Label |
|:---------:|:-----------:|
|1| Parked Car |
|2| Empty Space |

#### Aerial
| Class Num | Class Label |
|:---------:|:-----------:|
|1| Parked Car |
|2| Empty Space |
