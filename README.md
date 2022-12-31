# parking_detector
Yolov5/7 Parking Lot Vehicle Detection

# Goal
Measure the occupancy of a 

# Detection

Detection has the main target of assessing locations of vehicles, but additional options are useful for improving accuracy of 
Yolov5.

## Weight Naming Scheme:

yolov **$MODELVERSION** **$MODELSIZE** _ **$TRAININGSET**.pt
#### Example:
*yolov7-e6_coco.pt*

## Model Versions:
- `5` - Yolov5 generation of models
- `7` - Yolov7 generation of models

## Model Sizes:
- `` - Default model, smaller size
- `x` - Increased size of default model
- `-w6` - Increased size from x models
- `-e6` - Largest available model

## Training Sets:
- `coco` - [COCO2017](): Standard object detection & instance segmentation set for large scale pretraining including vehicles & people
- `pklot` - [PARKINGLOT](): Vehicle & Parking space object detection set 
- `aerial` - [AERIAL](): Aerial views of parking lots

# Tracking

Basic tracking is necessary to measure the amount of time vehicles are stopped, as including moving vehicles as in the parking space clustering could quickly interfere with appropriate results. However, movement tracking is not required except in these few extensions where tracking of moving vehicles may be useful.

1. `Stopped but not Parked Vehicles` - Distinguishing stopping at lights, traffic jams, stop signs, etc from longer term parking is necessary to limit spurious inclusions in the clustering algorithm. Tracking of vehicle paths should allow more detailed elimination of temporarily stopped vehicles.
2. `Traffic Patterns` - Much can be gleaned from time series analysis of the population of parked vehicles, but understanding the kinematics of moving vehicles within the scene requires a sense of direction, which requires at least a basic tracking algorithm.
3. `Tracking Pedestrians` - It is simple to add humans to the available detection classes, checking movement of pedestrians to gather data about interactions with cars or busy sections of the scene would be done best with a more reliable tracking mechanism.

## Options

- `stopped` - Track stopped vehicles 
- `nearby` - Track vehicles by Euclidian Distance exclusively, accuracy improves with framerate
- `norfair` - Track vehicles with [Norfair]() tracker, requires framerate > 5fps

# Clustering

Centers of detected objects/instances 

## Algorithms
- `HDBSCAN` - DBSCAN variant for fast clustering, especially of non circular clusters
- `MAHAL` - Malahanobis distance tracking for elliptical clusters
- `KMEANS` - Mean separation distance clustering for circular clusters

# Feature Extraction:



## Stopping Times

##

# Production

## Framerate:

- `minimal` - Single shot every 20 seconds, only tracks stopped vehicles
- `base` - 2 FPS, Faster acquisition of stopped vehicles, only allows basic tracking of slow vehicles
- `fast` - 10 FPS, Computationally more expensive, faster acquisition of stopped vehicles, better tracking
- `max` - Dependent on system specifications, process new image as soon as previous determination is made
- `custom` - Custom framerate, completely up to the user but may cause significant inaccuracy. Numbers larger than max framerate will reduce to the max framerate

## Reset

Frequency with which to reset cache, will maintain previous outputs but restarts system 

- `hourly` - reset every hour, useful if memory issues cause crashes after running for less than 24 hours
- `daily` - default, resets every night
- `weekly` - maximum duration between resets

- `soft` - resets algorithm only
- `hard` - default, reset entire device

# References

