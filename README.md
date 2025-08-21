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

## Message types


Each message exchanged between the master and the markers follows the format shown in Table 1. The message begins with a start byte (SB), followed by the source address byte (SA), destination address byte (DA), message code byte (MC), message data (MD), and ends with an end byte (EB). As shown, the size of each segment is 1 byte, except for the message data. It is established that the maximum length of the message data, together with the message code, is 8 bytes. Therefore, the data length can be up to 7 bytes and may be shorter depending on the message type.


<p align="center">
  <img src="https://github.com/user-attachments/assets/ac14fe27-98e9-41ee-83b6-8096d3176405" alt=" Figure 3: Format poruke" width="50%"/>
 <p align="center"><strong> Table 1.</strong> Message format between master and marker.
</p>


The message exchanged between the computer and the master device follows the format shown in Table 2. below. The difference compared to the previous table is the byte that indicates the active marker address (AMA). This byte is included within the message data, which means that the maximum data size in this case is 6 bytes.


<p align="center">
  <img src="https://github.com/user-attachments/assets/664ce6de-a148-4a6a-a208-2047e7b8c217" alt=" Table 2: Format poruke PC_master" width="60%"/>
 <p align="center"><strong> Table 2.</strong> Message format between computer and master.
</p>

The implemented messages are briefly described below.

**Master Presence** is a type of message through which the master device notifies the slaves (active markers) in the network of its presence. This indicates that the user intends to start the trajectory acquisition process. In this way, the active markers initiate their initial configuration (initialization). The message is sent every two seconds during the process, except during triggering, when it is paused.

**Marker Presence** is a message sent by an active marker once (the first time after receiving the master presence message in the network). This message informs the master of the active marker’s address in the network, as well as the total number of active markers connected.

**Frame Configuration** is a message used to configure the activation times of the active marker’s LEDs during recording.

The message consists of a series of elements formatted as shown in Table 3. The field NR (number of repetitions) represents the number of consecutive frames that share the same configuration. The fields nTD (n time delay) represent the activation delay times for each of the colors (R, G, and B), while the fields nTON (n time on) represent the pulse durations for each color. Each field is 1 byte in size. The maximum number of such configurations that can be sent to the marker is 255, since the size is encoded with one byte.


<p align="center">
  <img src="https://github.com/user-attachments/assets/2a0b7ebe-6320-43ca-98d9-cc1edcc2efc3" alt=" Table 3: konfiguracija frame" width="60%"/>
 <p align="center"><strong> Table 3.</strong> Frame configuration format.
</p>

**Intensity Configuration** is a message used to configure the intensity of the active marker’s LED, as described earlier. The message is sent from the master to the marker and consists of data with a length of three bytes, where each byte represents the intensity value of one of the colors (R, G, B).

**Trigger Configuration** is a message consisting of n consecutive bytes, where each byte represents the time interval between two frames. The message is sent from the computer to the master device, which stores the configuration values and later uses them during the triggering process.

**Trigger Start** is a message that indicates the start of triggering. The message contains the delay time before the triggering begins relative to the time the message is received (1 byte), as well as the triggering type (1 byte), whose meaning was described earlier.

It can be seen that the messages for frame configuration and triggering are of variable length and are often longer than 8 bytes. To ensure more reliable and efficient transmission of these messages, a protocol similar to the (E)TP protocol has been implemented. In this work, the protocol is referred to as the Long Message Protocol and is described below.

## Long Message protocol

**The Long Message Protocol** (LMP) operates similarly to the (E)TP protocol. It is a protocol designed for transmitting data whose length exceeds the maximum packet size allowed by the communication channel. The data is divided into multiple smaller packets before being sent. The timing diagram of the messages is shown in Figure 3.


<p align="center">
  <img src="https://github.com/user-attachments/assets/3a7c79e8-9583-4c84-97ed-f93b2b9c77b1" alt=" Figure 3: Vremenski dijagram" width="80%"/>
 <p align="center"><strong>Figure 3.</strong> Timing diagram of the Long Message protocol.
</p>


At the start of communication, the sender transmits an RTS message (Ready to Send) to the receiver. This message contains the total number of bytes the sender intends to transmit. The sender then waits for a CTS message (Clear to Send) from the receiver. Upon successful reception of CTS, the sender transmits packets of 8 bytes containing the original message data, where the first byte is the packet sequence number.

After sending the last packet, the sender sends an END message indicating the end of transmission. Following this, the receiver replies to the sender with an ACK message, confirming successful (or unsuccessful) reception of the packets.

If either device delays the response longer than the maximum allowed time, the connection is terminated without sending a termination message to the other device. In the event of any other error, the device sends an ABB message (Abort) to terminate the packet exchange.

Based on the above, message types have been added, with their data provided in Table 4.


<p align="center">
  <img src="https://github.com/user-attachments/assets/dde7229c-d66e-475d-9435-d3a3fece4f5c" alt=" Table 4: Tipovi LMP poruka" width="80%"/>
 <p align="center"><strong> Table 4.</strong> Types of LMP messages and brief description.
</p>

Based on the described scenario, finite state diagrams were created for the receiver and the transmitter (shown in Figure 4.) involved in communication via the LMP. These diagrams served as the basis for the development of the software LMP module.


<p align="center">
  <img src="https://github.com/user-attachments/assets/c48d30b4-8579-4423-9a47-5db8adc17592" alt=" Figure 4: Dijagram FSM" width="80%"/>
 <p align="center"><strong>Figure 4.</strong> Finite state machine diagrams for LMP (a- sender, b - receiver).
</p>


# Physical layer of IR communication


For the purpose of communication between the master device and the active markers, a physical layer for infrared communication was developed. The implemented protocol is similar to the IR Remote Control protocol. Data is transmitted byte by byte. A timing diagram of the transmission of a single byte is shown in Figure 5.




<p align="center">
  <img src="https://github.com/user-attachments/assets/847bbace-2a64-4fba-aabb-f901fa04259f" alt=" Figure 5: Dijagram FSM" width="80%"/>
 <p align="center"><strong>Figure 5.</strong> Timing diagram of sending one byte via IR channel.
</p>

Communication is carried out by transmitting IR light pulses ($I_n$, $n = [0,8]$) with a duration of $\boldsymbol{T_i}$. To transmit a single byte (8 bits), 9 pulses are sent. The first pulse indicates the beginning of the byte transmission. Each bit of the byte is encoded using the time interval between consecutive pulses ($T_{Bn}$, $n = [0,8]$).

The bit value "1" is encoded with a time interval of $\boldsymbol{T_1}$, while the bit value "0" is encoded with a time interval of $\boldsymbol{T_0}$. Naturally, due to potential timing discrepancies between the transmitter and the receiver, upper and lower bounds for the encoded values are defined (+/- N (%)).

The advantage of this transmission method lies in simplified error detection. If a pulse is not received, the receiver can detect an error based on the time interval between the remaining pulses. The main drawback is the non-constant data transmission rate, which depends on the values of the transmitted bits. The minimum transmission speed is defined by the following formula:

<br>


<div align="center">
  <img src="https://latex.codecogs.com/png.image?\dpi{120}f%20=%20\frac{1}{9T_i%20+%208\max\{T_1,%20T_0\}}%20[bps]" alt="Transmission rate formula"/>
</div>
<br>


Where:  
- <i>T</i><sub>i</sub> is the duration of each IR pulse  
- <i>T</i><sub>1</sub> is the interval for bit "1"  
- <i>T</i><sub>0</sub> is the interval for bit "0"


