# Overview

## Introduction
2D echocardiography (echo) is the most widely used imaging modality for measuring left ventricular function. It is noninvasive, low cost, fast turnaround time, and has high temporal resolution. Measurement of left ventricular ejection fraction (LVEF) is a common reason for echo, but output of LV boundaries are in discrete 2D planes. 3D LV geometry needs to be reconstructed from those planes to quantify LV wall motion, acceleration, and strain, or to carry out flow simulations.

The normal LV is shaped as a prolate ellipsoid. 

MRI, 3D echo, and CT can all produce 3D surface data, but typically require longer acquisition times (>10 min) or higher costs. They also all suffer from low temporal resolution (~20-30 frames per second (fps)). Differentiation between cardiac phases and accurate quantitative measures on LV motion require high temporal sampling of ventricular borders. This gives 2D echo an advantage over the other modalities due to its high temporal resolution (~250 fps down to ~50 fps to resolve whole LV). 

#### Views
Standardized LA projections, according to the American Society of Echocardiography, is defined as apical two-chamber (A2C), three chamber (A3C), and four-chamber (A4C), and standardized SA projections are basal, mid, and apical.

#### Glossary
**Whole heart reconstruction**: refers to the process of generating a 3D model of the entire heart from a series of 2D medical images, such as CT or MRI scans. This technique is also known as **"cardiac 3D reconstruction"** and can be used to visualize the heart in 3D and analyze its structure, function, and pathology. This approach can be useful for diagnosis, treatment planning, and research in various cardiovascular conditions.

**Spatial interpolation**: is a technique used to estimate values at unsampled locations within an area or a volume based on the available data. In the context of 3d cardiac reconstruction, it's usually the usage of available cross-sectional data obtained from LA and SA echo projections to generate surface points as the required spatial resolution to construct a 3D model of the heart.

The process of whole heart reconstruction typically involves multiple steps as below. It requires specialized software and expertise in medical image processing and cardiac anatomy:
- image preprocessing
- segmentation
- registration
- 3D modeling

## Goal
The primary goal of this repository is to explore various methods that allow 3D reconstruction of the heart from 2D echocardiography images.

## Processing
Echo data are usually in video format and typically show the motion of the LV boundary througghout one cardiac cycle. Processing of these images usually entail extracting the frames of the video as individual images using video processing libraries. OpenCV is an open source library that is commonly used in processing image and video files and can be used in this scenario to extract the frames of the video as a 2D matrix of RGB data. The matrix stores RGB values of each pixel in 2D matrix format of size 3w x h where w and h are the number of pixels along the width and height of the frame-shot, respectively. 

## Methods to explore and compare
Pix2Voxx++ from [Stojanvoski D. et al. 2022](https://arxiv.org/abs/2207.13424)

### Pix2Voxx++ (PiVox)
This method uses two types of synthetic data: 1) segmentation dataset containing binary tissue masks (segmentations) on 2D standard views. Masks are simulated by slicing 3D computational models (allows for 3D ground truths for evaluation) and 2) synthetic US dataset containing synthetic 2D standard US views, generated from tissue masks.

### PiVox/Accurate (PiVox/Fast)

### PiVox/Fast Network
Proposed by Xie et al. as a lighter weight, albeit lower performing, variant of the PiVox/Accurate network (PiVox/Fast) that uses ResNet-18 instead of ResNet-50, decreased de/convolution kernel sizes, and removal of the refiner module.

### E-PiVox from Stojanvoski D. et al.
Adaptation of PiVox networks via addition of 3D convolution at end of encoder module along the input image dimension to reduce decoder to a single branch for any number of input images.

### 3d reconstruction algorithm from [Kim T. et al. 2021.](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC8047352/)


### Summary of methods
|Method|Category|Description|Supervision|
|---|---|---|---|
|Pix2Vott++|deep convolutional neural network (CNN) architecture|specifically designed for 3D reconstruction from 2D images|Supervised|
|ResNet-18|deep convolutional neural network (CNN) architecture|general-purpose architectures for image classification and other tasks|Supervised|
|ResNet-50|deep convolutional neural network (CNN) architecture|general-purpose architecture for image classification and other tasks|Supervised|

## Datasets
- ShapeNetCore: covers 55 object categories with over 51,300 unique 3D models. Used to perform comparisons of reconstruction techniques.

## Evaluation
- Thresholded Intersection over Union (IoU) is used to assess reconstruction accuracy
- 

## References
1. Deep Dive – Why 3d Reconstruction may be the next tech disruptor 
2. Automatic segmentation of the left ventricle in echocardiographic images using convolutional neural networks 
3. 3D High-Resolution Cardiac Segmentation Reconstruction from 2D Views using Conditional Variational Autoencoders 
4. Automated Three-Dimensional Reconstruction of the Left Ventricle From Multiple-Axis Echocardiography 
5. Efficient Pix2Vox++ for 3D Cardiac Reconstruction from 2D Echo Views 
