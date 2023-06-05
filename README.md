# Overview

## Introduction
2D echocardiography (echo) is the most widely used imaging modality for measuring left ventricular function. It is noninvasive, low cost, fast turnaround time, and has high temporal resolution. Measurement of left ventricular ejection fraction (LVEF) is a common reason for echo, but output of LV boundaries are in discrete 2D planes. 3D LV geometry needs to be reconstructed from those planes to quantify LV wall motion, acceleration, and strain, or to carry out flow simulations. 

The normal LV is shaped as a prolate ellipsoid. 

MRI, 3D echo, and CT can all produce 3D surface data, but typically require longer acquisition times (>10 min) or higher costs. They also all suffer from low temporal resolution (~20-30 frames per second (fps)). Differentiation between cardiac phases and accurate quantitative measures on LV motion require high temporal sampling of ventricular borders. This gives 2D echo an advantage over the other modalities due to its high temporal resolution (~250 fps down to ~50 fps to resolve whole LV). [Rajan et al. 2016](https://pubmed.ncbi.nlm.nih.gov/26548948/). However, the challenge is the reconstruction of a 3D model from few, spatially cross-sectional data. 

#### Calculating Ejection Fraction (EF) per [YouTube video from Nonstop Neuron](https://www.youtube.com/watch?v=OJWJ9MG4yYU)
Let's begin by defining a few terms:
- **End diastolic volume (EDV)**: quantity of blood in the ventricles at the end of diastole (heart relaxes and fills with blood). Normal EDV is 120 ml for each ventricle.
- **Systole**: This is when heart contracts and some of the blood is pumped out. 
- **Stroke volume (SV)**: Volume of blood that is pumped out during systole. Normal SV is 70 mL for each ventricle. 
- **End systolic volume (ESV)**: Volume of blood that is remaining in the ventricle at the end of systole. Normal ESV is 50 mL since normal EDV (120 mL) minus normal SV (70 mL) is 50 mL.
- **Ejection fraction (EF)**: The fraction of end-diastolic volume that is ejected during systole. Thus, normal EF = SV/EDV or normal EF = 60% (70mL/120mL). 

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

**Spatial orientation**: Per [Rajan et al. 2016](https://pubmed.ncbi.nlm.nih.gov/26548948/), the spatial orientation of the six, standardized views can be approximated as follows:
|Section|View|Orientation|Apex to base length|
|---|---|---|---|
|LA|A4C|α = 0|n/a|
|LA|A2C|α = π/3|n/a|
|LA|A3C|α = 2π/3|n/a|
|SA|basal|α = 0| z=0.0|
|SA|mid|α = 0| z=0.5L|
|SA|apical|α = 0| z=0.8L|

**Computational fluid dynamics (CFD)**: The reconstructured, 3D LV is used as an input for CFD simulations of the LV flow. It's important for the motion of the LV to be smoothened for the input to generate smooth flow rates. 

**Video dimensions**: When processing videos using OpenCV, they will typically have the following dimensions:
- **Time**: represents the number of rames in the video, indicating the temporal aspect.
- **Height**: refers to the vertical resolution of each frame, representing the number of rows of pixels
- **Width**: refers to the horizontal resolution of each frame, representing the number of columns of pixels
- **Channels**: represents the number of color channels in each pixel. For grayscale videos, the number of channels is usually 1 (indicating intensity). For color videos, the number of channels is typically 3 (representing Red, Green, and Blue color channels). 

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

### 3d reconstruction algorithm used in Kim 2021, previously developed by [Rajan et al. 2016](https://pubmed.ncbi.nlm.nih.gov/26548948/)

### Simple Ellipsoid reconstruction
This section describes a simple method of doing a 3d reconconstruction using two, 2D echocardiography views: A4C and PSAX. As referenced in other articles, the shape of the left ventricle resembles that of an ellipsoid. Because we have a LA view from A4C and a SA view from PSAX, we can do a simplified reconstruction by calculating the cross-sectional shapes (i.e. circle and an ellipse) that form the ellipsoid since it is symmetrical about three mutually perpendicular axes that intersect at the centre.

<div align="center">

**The formula is defined as:** 
  
$$
\frac{x^2}{a^2} + \frac{y^2}{b^2} + \frac{z^2}{c^2} = 1
$$

In this formula, x, y, and z are variables, and a, b, and c are constants.
</div>

**Shapes dictated by constants:**
|Shape|Scenario|Comment|
|---|---|---|
|Sphere|a=b=c|Intersection with any plane passing through it is a circle|
|Ellipsoid of revolution (aka spheroid)|a=b, but different from c|Formed by revolving an ellipse about one of its axes|
|Oblate spheroid|a+b>c|Formed by revolving an ellipse about its minor axis|
|Prolate spheroid|a+b<c|Formed by revolving an ellipse about its major axis|

**Ellipse**
The ellipse is one of the cross-sectional shapes from an ellipsoid. The **major axis** is the line segment going through the farest points on an ellipse. The **minor axis** is the segment going through the closest. 

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
6. [Automated Three-Dimensional Reconstruction of the Left Ventricle From Multiple-Axis Echocardiography](https://pubmed.ncbi.nlm.nih.gov/26548948/)
7. [3d Reconstruction of the Left Ventricle From Multiple-Axis Echocardiography Images](https://asmedigitalcollection.asme.org/IDETC-CIE/proceedings-abstract/IDETC-CIE2014/46285/V01AT02A046/256527)
