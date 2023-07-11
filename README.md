# DL8TQ2_SensorModalities
Homework assignment for Sensor Modalities course.
The solution integrates medical ultrasound machines with 3D Slicer, providing real-time image transfer from the machine to the capture computer.

A video demo is available [here](https://drive.google.com/file/d/1m9kUvyMkpxUQMSjhG2rLEMWBm1b6h29R/view?usp=sharing)

## Prerequisites
- 3D Slicer: The solution is implemented as a 3D Slicer module.
- PLUS Toolkit: The solution launches a PlusServer.exe in the background, which is part of the PLUS toolkit.
- Hardware: Any PLUS-compatible imaging device to serve as input. In this project, I used an Epiphan DVI2USB3.0 video grabber to capture the video output of a medical ultrasound machine.

## Installation
The installation is done by adding the module path in Slicer:
- Go to Edit / Application Settings / Modules
- find the BLUELungUltrasound.py file in the cloned local repository
- Drag and drop the file in the "Additional module paths" section
- Restart Slicer

After restart, the module should be visible under IGT/BLUE Lung Ultrasound

## How to use:
After the module loads, check the following:
- PLUS Server Config file: Select the appropriate PLUS config file for the device you are using (IMPORTANT: The communication port needs to be 18944). The default config file included in the repository is for an Epiphan DVI2USB3.0 device.
- PlusServer.exe: Select the appropriate PlusServer.exe that you want running in the background. By default the solution selects the first available PlusServer.exe on your local computer.
- Make sure the hardware is connected and ready to be used

Once everything is ready, click "Start PLUS Server". In a few moments, your Slicer should be receiving live data. With the default configuration file, the node receiving data is called "Image_Reference" - if you click "Set Views", the red slice view should be set to display this node (note: the "Set Views" button currently only works with the node name "Image_Referene")
