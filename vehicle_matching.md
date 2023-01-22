# Matching Images of Individual Vehicles AKA 
# [`Vehicle Re-identification`](https://paperswithcode.com/task/vehicle-re-identification)

## Motivation
Many applications exist for individual monitoring and measurement of vehicle via traffic camera video feeds. 
To extend these tools between cameras, being able to extend tracking to vehicles from one camera to another is essential.
This process is an extension of re-identification, or ReID, which has been studied extensively in [`object tracking`](https://paperswithcode.com/task/object-tracking), especially using [`detections of people`](https://paperswithcode.com/task/person-re-identification)
The tracking of vehicles come with significant ethical considerations, the intent of this project is *NOT* to match vehicles with individuals, 
such as with unique features or license plate identification,
but instead to track the travel of specific vehicle makes/models within a given area.
This means the process can be more easily fooled by superficially similar vehicles traveling near each other, 
for example the system may fail to distinguish between two white Chevy trucks,
but this reduces the potential for abuse in monitoring locations of individuals.


## Example Applications
- Tracking location of vehicles fleeing crime scenes
- Measuring traffic inflow/outflow over large areas
- Monitoring speed by measuring time lapsed between two camera detections

## Methods
- Supervised Neural Networks
- Semi Supervised Networks
- Unsupervised Networks
- Position Prediction Networks

## Relevant Papers

## Relevant Datasets
