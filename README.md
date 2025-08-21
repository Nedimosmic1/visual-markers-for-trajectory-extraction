# Visual markers for trajectory extraction

<p style="text-align: justify;">
 
 The following section outlines the implementation of the software component of the active marker system. The system is designed such that the active markers are controlled via a dedicated computer application, primarily intended for testing and evaluation purposes. In the final deployment, this application is to be integrated with a camera control and image processing system. Communication between the application and the master device is established via a serial bus. The master device consists of an Arduino Uno microcontroller coupled with a hardware module for infrared communication. Based on commands received from the computer, the master device manages the network of active markers. Each active marker functions as a slave device, receiving and executing commands issued by the master.
The figure 1. illustrates the setup of the described system.

</p>



<p align="center">

<img src="https://github.com/user-attachments/assets/a66ca8bc-390f-49d9-802b-3b671a5d6c70" alt="Figure 1: Smart_markers" width="70%"/>
 <p align="center"><strong>Figure 1.</strong> System setting for acquisition of object movement trajectory.

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
 <p align="center"><strong>Figure 2.</strong> System operation diagram.
</p>

In the case of the active SM2 markers, this process cannot be performed via IR communication. Therefore, upon receiving the message, they will indicate it by turning on an LED. The addresses of these markers must be manually entered into the computer application. Once the active markers have been detected, the process can continue.

Before beginning the acquisition of the trajectory of the object of interest, it is necessary to configure the active markers. After configuring the active markers, the triggering configuration is carried out, which relates to the master device. The order of these configurations is not important. Once the configuration is successfully completed, the system is ready to begin recording the object's trajectory.

At the start of recording, the triggering of the active marker network must be initiated. The system supports two triggering modes: time-based triggering and external signal triggering.

Time-based triggering requires the computer to send the triggering times to the master device, which then uses this data during the triggering phase. External signal triggering is performed by applying a signal to one of the master device’s pins.
The configuration of the active markers consists of two parts, namely:

- LED intensity configuration,
- Frame configuration.

**LED Intensity Configuration** is performed by sending a configuration message containing intensity values from the computer, through the master device, to the active marker. The intensity configuration consists of three parameters: the intensity of the red, green, and blue LEDs. Although it was previously stated that the intensity of the red LED is constant, the system is designed with the potential for future hardware upgrades, allowing for such changes without altering the message format.

**Frame Configuration** involves setting the activation time and pulse duration of each LED for every frame during the object recording process. For each frame, it is possible to define the LED activation delay relative to the triggering time, as well as the duration of the LED being turned on. This is done by sending a frame configuration message.

After configuring the active markers, as mentioned earlier, the computer needs to send the frame triggering configuration to the master device. This configuration consists of predefined frame activation times when time-based triggering is used. If external signal triggering is employed, this configuration is not required. After configuring the master device, the acquisition of the object's trajectory can begin. The activation of the markers is performed by sending a message to start the system triggering. This message contains the type of system triggering as well as the delay for starting the triggering from the moment this message is received.


