# Visual-markers-for-trajectory-extraction
 The following section outlines the implementation of the software component of the active marker system. The system is designed such that the active markers are controlled via a dedicated computer application, primarily intended for testing and evaluation purposes. In the final deployment, this application is to be integrated with a camera control and image processing system. Communication between the application and the master device is established via a serial bus. The master device consists of an Arduino Uno microcontroller coupled with a hardware module for infrared communication. Based on commands received from the computer, the master device manages the network of active markers. Each active marker functions as a slave device, receiving and executing commands issued by the master.
The figure illustrates the setup of the described system.
<p align="center">
  <img width=250 src="0.doc/artwork/">
</p>
