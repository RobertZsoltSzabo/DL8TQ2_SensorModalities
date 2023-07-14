# Displaying live medical ultrasound video data in 3D Slicer
Author: Róbert Zsolt Szabó, Óbuda University (2023)

## Introduction
The purpose of the project is to receive and display the live video feed of a medical ultrasound machine in real time on a separate computer. This is desirable in cases when we want to record ultrasound images for later processing, or in case we want to perform real time image processing on the live ultrasound feed.

## Proposed solution
I propose a solution that uses an external video capture card to receive data from the ultrasound machine and send it to the processing computer, then we use 3D Slicer to show the live ultrasound video. The solution should work with any ultrasound machine capable of providing a video output. 

### Video Capture
In order to provide the video stream data to the processing computer, we need an external video capture card. In this project we used an [Epiphan DVI2USB 3.0 Video Grabber](https://www.epiphan.com/products/dvi2usb-3-0/). The device doesn’t simply captures and sends raw data, but sends it as preprocessed images. This is beneficial as we don’t need to rely on the resources of the capture computer, which allows us to use those resources for other purposes (e.g.: live image processing). I connected the DVI output of the ultrasound machine to the input of the Epiphan, and the output of the epiphan to a USB 3.0 port on the processing computer.

### OpenIGTLink
[OpenIGTLink](http://openigtlink.org/about.html) is an open-source network communication interface specifically designed for image-guided interventions. In this project I use OpenIGTLink for communication between the Epiphan video grabber device and 3D Slicer.

### PLUS Toolkit
[The PLUS Toolkit](https://plustoolkit.github.io/about) is free, open-source library and applications for data acquisition, pre-processing, calibration, and real-time streaming of imaging, position tracking, and other sensor data [TODO: ref ]. In this project, I use PLUS to send the data coming from the Epiphan video grabber to Slicer over OpenIGTLink, as the Epiphan device used is supported out of the box by the PLUS Toolkit.

### 3D Slicer
[3D Slicer](https://www.slicer.org/) is a free, open source software for visualization, processing, segmentation, registration, and analysis of medical, biomedical, and other 3D images, and also in image guided therapy. Although using Slicer is not strictly necessary to simply receive and display images, using it allows us to readily perform additional medical image processing tasks using 3D Slicer’s extensive toolkit. Since this is exactly what we want to enable by capturing the video feed, this is a logical extension of this project. Additionally, 3D Slicer also comes with built-in support for OpenIGTLink and PLUS, both of which I will be using during the implementation. 3D Slicer will serve as the GUI for the application, both for visualizing the incoming data and for controlling the application.

## Implementation
The solution is implemented as a custom scripted module in Slicer. The source code submitted for the assignment is publicly available in this repository.

The physical setup of the solution is visible below. We can see the ultrasound machine connected to a laptop through an Epiphan DVI2USB3.0 video grabber:
![alt text](https://github.com/RobertZsoltSzabo/DL8TQ2_SensorModalities/blob/main/Documentation/Images/Physical_setup.jpg "Physical implementation")

The module sets up an OpenIGTLink client at startup in the background, which listens to a predefined port on localhost. Then, the module allows to set up and control a PLUS server, which while running, acts as an OpenIGTLink server, sending data to the same predefined port. The full communication chain is implemented as the following:

![alt text](https://github.com/RobertZsoltSzabo/DL8TQ2_SensorModalities/blob/main/Documentation/Images/Communication_Diagram.png "Communication architecture")

Control of the PLUS server is realized with a simple GUI within Slicer:
![alt text](https://github.com/RobertZsoltSzabo/DL8TQ2_SensorModalities/blob/main/Documentation/Images/Slicer_GUI.PNG "Slicer module GUI")

The GUI offers the following features:
- **Select PLUS configuration**: The user needs to select a PLUS configuration specific to their connected device. Since the device is the Epiphan video grabber in our project, the solution provides a default configuration file for this device. The configuration file defines the Slicer node name the incoming data will be captured in.
- **Select PlusServer.exe**: The user can select the PlusServer executable file that they wish to use for running the PLUS server. By default, the solution looks for a PlusServer.exe on the computer and pulls in the first match. The option can still be relevant in case the user has multiple different PLUS builds, but the default should work in most cases. Having PLUS installed is a prerequisite of this tool.
- **Start / Stop PLUS Server**: A toggle button which starts or kills a background subprocess running the previously selected PlusServer.exe.
- **Set Views**: A button that displays and centers the incoming data in a Slicer view.

Once running, incoming data can be viewed in Slicer in real time. The default incoming node name is “Image_Reference”:
![alt text](https://github.com/RobertZsoltSzabo/DL8TQ2_SensorModalities/blob/main/Documentation/Images/displayed_US_image.png "Live ultrasound image displayed in Slicer")

Short video demonstration of the solution working in real time is available [here](https://drive.google.com/file/d/1m9kUvyMkpxUQMSjhG2rLEMWBm1b6h29R/view?usp=sharing)

## How to use
The following section details how to set up the module for independent use.

### Prerequisites
- 3D Slicer: The solution is implemented as a 3D Slicer module.
- PLUS Toolkit: The solution launches a PlusServer.exe in the background, which is part of the PLUS toolkit.
- Hardware: Any PLUS-compatible imaging device to serve as input. In this project, I used an Epiphan DVI2USB3.0 video grabber to capture the video output of a medical ultrasound machine.

### Installation
The installation is done by adding the module path in Slicer:
- Go to Edit / Application Settings / Modules
- find the BLUELungUltrasound.py file in the cloned local repository
- Drag and drop the file in the "Additional module paths" section
- Restart Slicer

After restart, the module should be visible under IGT/BLUE Lung Ultrasound

### How to use:
After the module loads, check the following:
- PLUS Server Config file: Select the appropriate PLUS config file for the device you are using (IMPORTANT: The communication port needs to be 18944). The default config file included in the repository is for an Epiphan DVI2USB3.0 device.
- PlusServer.exe: Select the appropriate PlusServer.exe that you want running in the background. By default the solution selects the first available PlusServer.exe on your local computer.
- Make sure the hardware is connected and ready to be used

Once everything is ready, click "Start PLUS Server". In a few moments, your Slicer should be receiving live data. With the default configuration file, the node receiving data is called "Image_Reference" - if you click "Set Views", the red slice view should be set to display this node (note: the "Set Views" button currently only works with the node name "Image_Referene")

