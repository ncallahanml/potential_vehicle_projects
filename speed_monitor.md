# Monitoring Speed from a Fixed Position Camera

## Motivation
When adjusting speed limits, considerations must be taken for the current traffic conditions and traveling speeds for a given roadway.
Additionally, when closing roadways and adding detours, planning expansion, and monitoring ideal times for construction, 
the speed and prevalence of vehicles going at speed on specific sections of road are important for making well informed decisions.
However, these projects do not need a roadway to be permanently monitored, and may be out of the purview of established traffic cameras. 
To manually monitor roadways is time consuming and subsequently expensive, so to compensate different systems are commonly used for tracking vehicle movement.
However, these include a host of innacuracies that limit their utility, and they can be challenging to construct.

An example of a common method for measuring speed: 
<&emsp> Two tubes are inflated across a roadway, a short distance from one another, along with a device measuring their pressure.
- As vehicles pass over the tubes, pressure increases significantly.
- The tubes pressures can be measured independently to assess the time difference in the vehicle's wheels crossing the fixed distance between the tubs
- This allows a shorthand calculation of speed
<&emsp> Problems:
- Error can be high, as only a single point is measured
- Vehicles can cross from both directions simulataneously, falsifying readings
- Measuring the prevalence of vehicles is biased by the number of axles,
it is challenging to distinguish between two nearby cars and one vehicle pulling a trailer
- This problem is compounded for semis 
- Vehicles may inherently slow down when approaching the apparatus, further biasing measurements

Here we propose an alternative approach: Setting a single camera on the side of the roadway at a fixed position, 
neural networks will be used to track the number of vehicles traveling across a roadway.
Using outputs from the neural network, the speed (both in cardinal & ordinal terms) of the vehicles should be tracked to gain necessary roadway statistics.
The challenge is in converting speed values in `pixels * framerate` to `miles / hour`.

## Potential Solutions
- Road Markings
- Wheelbase Averaging
- Empty Road Assumptions
