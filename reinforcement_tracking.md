# Reinforcement Tracking

When implementing object tracking on a complex traffic scene, a few glaring issues came to the forefront with out-of-the-box object multi-object trackers (MOTs).
Most of these issues fit into the following categories:
**Interoperability**:
Some trackers are designed as standalone systems, they fail to integrate with other models and trackers and can be challenging to draw tracking information/coordinate
information from for use with extensions of the data. Even trackers that handle multiple models well lack some clarity when it comes to processing inputs and formatting 
outputs - significant work has to be repeated outside the tracker to get baseline information about the tracking predictions.

**Flexibility**:
We are ultimately looking for a tracker that will quickly perform well on a variety of related but fundamentally different scenes. Not only that, conditions within
the scenes will change regularly, but most trackers perform well in a specific band that may not be representative of all conditions in a scene, and may be completely
outside the conditions of an alternative scene.

**Speed Inconsistency**:


**Stopped Vehicles**:
Stopped vehicles should be the easiest case to handle, but trackers frequently assume that movement is occurring. This can mean some trackers do exceptionally well
in a difficult environment, but their results appear very poor due to their inability to handle simpler cases. The case of stopped cars can be handled separately, 
though downstream consequences may occur as the tracker states assume all valid predictions are being entered into the updated state.

**Low Framerate Handling**:
Frequently, high quality demonstrations for trackers feature complex scenes that include all of the above elements, and still perform well. The secret sauce here is
not exceptional software - it is high framerates that mitigate difficulties trackers often face. The first important observation here - if all objects are being detected
in every scene - a common case - then tracking is as simple as matching the nearest objects to one another, and no advanced tracker is even necessary. Aside from this, most
the problems of stoppage and speed inconsistency are mitigated: when vehicles move such a small distance one frame to the next, the deviation in total movement is small.
Another way of phrasing this: almost all vehicles are nearly stopped, thus the gap between fast moving and slow moving vehicles does not raise an inconsistency issue. 

**Computational Complexity**:
Object detection networks have been increasing in both speed an accuracy at an astounding rate, but MOT performance has somewhat stagnated in the practical case. This
leads to another practical constraint that biases tracking toward the simple distance tracking method - pipelines are bottlenecked not by the large expensive networks
but by handling their outputs. This is compounded by trackers which perform large computations but are _not_ handled on CUDA hardware that has become the norm for
neural network applications, trackers which may not feature multithreading as well. Now, due to hardware development focusing on GPUs with slow but multicore processors,
trackers have decreased in actual speed but networks are far faster than ever.


# Motivation

**Non MOT Assumptions**:
A few additional assumptions are valid for our tracking scenes that are not necessarily utilized by typical MOTs.
- Fixed Pathing: cars travel through fixed paths in scenes. Some of this is almost guaranteed - hopefully we dont witness anyone traveling _through_ a concrete
barrier - but many of them are true in the average case, such as vehicles not reguarly changing lanes when traffic is moving at full speed. Deviations from this are
the topic of the anomaly tracking page, and for most applications trackers can be forgiven for missing triple lane changes, travel in the opposite direction of traffic, or unusually high speeds that are outside reasonable tracking parameters.
- Steady Size/Shape Difference: Many MOTs are trained on people, and may have inputs regarding model confidence or other scores, but do not necessarily assume that
the dimensions of a bounding box or the traits of the contained image will be consistent. This is logical for people, where stretching arms or opening an umbrella
would break both these principles, but it can be assumed that aside from weather conditions affecting global image conditions, vehicles will be almost identical one
frame to the next. One notable exception to this rule is in the case of heavy occlusion.
- Trend/Cyclicality Divergence: Again using the example of tracking human movement, people regularly stop and start, turn around, change speed, enter or exit the environment, etc. This is partially handled in the fixed pathing case, but here the global consistencies in vehicle movement are a priority. Vehicles are within a shared system, when some vehicles stop so do others, when everyone is moving quickly it is likely nearby cars are doing so as well. This further breaks down into cyclicality within a scene - due to traffic patterns, lights, and the inherent cause/effect relationships within traffic lanes. These highly predictable changes to the trend should be exploited by a fully functioning tracker, but macro level movement is not typically baked into MOTs.

**Long/Short Objectives**:
Greedy Objective: Match objects between two consecutive frames
- Analogue : predict the next move in a chess game

Global Objective: Measure the entire path of the vehicle
- Analogue : win the chess game