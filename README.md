# Visual markers for trajectory extraction

<p style="text-align: justify;">
 
 The following section outlines the implementation of the software component of the active marker system. The system is designed such that the active markers are controlled via a dedicated computer application, primarily intended for testing and evaluation purposes. In the final deployment, this application is to be integrated with a camera control and image processing system. Communication between the application and the master device is established via a serial bus. The master device consists of an Arduino Uno microcontroller coupled with a hardware module for infrared communication. Based on commands received from the computer, the master device manages the network of active markers. Each active marker functions as a slave device, receiving and executing commands issued by the master.
The figure 1. illustrates the setup of the described system.

</p>



<p align="center">

<img src="https://github.com/user-attachments/assets/a66ca8bc-390f-49d9-802b-3b671a5d6c70" alt="Figure 1: Smart_markers" width="70%"/>
 <p align="center"><strong>Figure 1.</strong> System setting for acquisition of object movement trajectory

</p>

Based on the above, the software component of the entire system can be divided into three parts:

    - Computer application for managing the active marker system
    - Master device software
    - Software for Active Markers System

For better synthesis and analysis of the software, it is necessary to describe the system's operation process and its functionalities.

## System workflow

Figure 2. shows a diagram describing the system operation. Upon starting the application, the master device, and the active markers, the master device begins sending an activity message to the active markers. If the active markers are SM1, upon receiving the master's activity message, they respond by sending a presence message back to the master. The master then forwards this message to the computer, thereby providing information about the connected active markers.



 
<p align="center">
  <img src="https://github.com/user-attachments/assets/185b419d-e82f-4867-b199-83c3a401bc84" alt=" Figure 2: Dijagram toka" width="80%"/>
 <p align="center"><strong>Figure 1.</strong> System operation diagram.
</p>

