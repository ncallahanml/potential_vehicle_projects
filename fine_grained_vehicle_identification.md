# Detecting Vehicle Types, Makes and Models
## Different Class Structures
### Broad Classes
Sedan | Truck | SUV

### Makes

### Models


### Colors


### Sizes

## Training Methods
Training the YOLO network is not difficult, however collection of data is imperative, 

### Existing Datasets

### Web Scraping

### Existing Networks

### Training YOLO
How to integrated standalone CNNs with the speed of YOLO detection classification

#### Standard
Training YOLO network with labeled images

#### Bootstrapped
Using an existing classifier's output to reclassify vehicles that have been correctly identified but not in an appropriate manner

#### Replacing Classifier
Swapping out the classifier network weights in YOLO

#### Sequential
Use YOLO for detections, CNN afterward for classification (inefficient)
