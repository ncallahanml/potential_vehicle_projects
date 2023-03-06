# Monitoring Speed from a Fixed Position Camera

## Motivation
When adjusting speed limits, considerations must be taken for the current traffic conditions and traveling speeds for a given roadway.
Additionally, when closing roadways and adding detours, planning expansion, and monitoring ideal times for construction, 
the speed and prevalence of vehicles going at speed on specific sections of road are important for making well informed decisions.
However, these projects do not need a roadway to be permanently monitored, and may be out of the purview of established traffic cameras. 
To manually monitor roadways is time consuming and subsequently expensive, so to compensate different systems are commonly used for tracking vehicle movement.
However, these include a host of innacuracies that limit their utility, and they can be challenging to construct.

An example of a common method for measuring speed:  

Two tubes are inflated across a roadway, a short distance from one another, along with a device measuring their pressure.
- As vehicles pass over the tubes, pressure increases significantly.
- The tubes pressures can be measured independently to assess the time difference in the vehicle's wheels crossing the fixed distance between the tubs
- This allows a shorthand calculation of speed
Problems:
- Error can be high, as only a single point is measured
- Vehicles can cross from both directions simulataneously, falsifying readings
- Measuring the prevalence of vehicles is biased by the number of axles,
it is challenging to distinguish between two nearby cars and one vehicle pulling a trailer
- This problem is compounded for semis 
- Vehicles may inherently slow down when approaching the apparatus, further biasing measurements

Here we propose an alternative approach: Setting a single camera on the side of the roadway at a fixed position, neural networks will be used to track the number of vehicles traveling across a roadway. Using outputs from the neural network, the speed (both in cardinal & ordinal terms) of the vehicles should be tracked to gain necessary roadway statistics.
The challenge is in converting speed values in `pixels * framerate` to `miles / hour`.

## Potential Solutions
- Road Markings
- Wheelbase Averaging
- Empty Road Assumptions

## Broad Approach
Estimating the speed in pixels for a vehicle is trivial, however estimating the speed in miles per hour (or an equivalent distance unit) is quite challenging as it is heavily dependent on properties of the scene. Fortunately, the time estimation is simple, given certain hardware availablility that - good or bad - is independent of the goal of the software presented in this project. This project assumes vehicle tracking is highly accurate, which is a fair assumption given the precursor assumptions on availability of hardware and _chosen camera angle_. For hardware estimations and **potential** solutions for cases where these assumptions are not satisfied, see the end of this document. For potential tracking solutions, see documents that should be available within this repository.

## Camera Placement
The placement of the camera is paramount to determining accurate speeds, and unlike previous projects which were designed to work on existing traffic cameras, the situation is justifiable here. The idea is that a mobile camera would be available, or that a camera could be erected on demand for projects which the accompanying measurements are useful. Ideally, the camera will be located such as to view the traveling vehicles from the side at a perfectly perpendicular angle to the direction of motion, without any objects crossing between the measured vehicles and the camera. This would suggest, based on the travel directions of two lane roads in the US, that a camera be placed to measure the right side of the vehicle.

If the camera were placed directly in line of the vehicle's motion, the speed could only be measured by estimating perspective shift as the vehicle moves toward or away from the camera. When measuring one vehicle, we want to minimize the overlap in traffic, and the best way to do this is to take an angle in three dimensional space that is orthogonal to the direction of motion, specifically from the top down so as to limit any potential for overlap. The difficulty in establishing a camera directly above a roadway for temporary measurements has this document assume the vehicle is measured from the side, which is not only more practical for setting up a camera but for identifying vehicles, where images of the sides of vehicles are more prevalent. Directly from the side does suggest that vehicles will be completely occluded at times.

## Framerate
The faster the framerate, the more computational power is required but the faster vehicles can be detected. Since we are assuming that speed is measured retrospectively, the real time detection is less important and therefore a variety of framerates can be used. However, trackers generally prefer faster framerates and anomalies caused by overlapping vehicles can be best resolved with higher framerates that are averaged over to reduce measurement error.

## Methods for Measuring Distance
### Wheelbase Tracking
[UC Berkeley Traffic](https://path.berkeley.edu/research/traffic-operations)
Somewhere in the above website is a study of traffic that suggests that the average wheelbase of vehicles traveling through an area is fairly consistent. This means such a value could be measured by object detection plus additional methods that track the vehicle wheels. Once a steady value is measured in a sample set and matched (either by hand or automatically) with specifications for the wheelbase of specific vehicles, this value can be used to measure distance to pixel conversion and turn pixel / s values to miles / hour with some straightforward conversions.

### Automated Length Matching


### Road Markings


### Posted Markings


## General Case

## Hardware Requirements

