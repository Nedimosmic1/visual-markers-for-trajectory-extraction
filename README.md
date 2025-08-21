# Visual markers for trajectory extraction

<p style="text-align: justify;">
 
 The following section outlines the implementation of the software component of the active marker system. The system is designed such that the active markers are controlled via a dedicated computer application, primarily intended for testing and evaluation purposes. In the final deployment, this application is to be integrated with a camera control and image processing system. Communication between the application and the master device is established via a serial bus. The master device consists of an Arduino Uno microcontroller coupled with a hardware module for infrared communication. Based on commands received from the computer, the master device manages the network of active markers. Each active marker functions as a slave device, receiving and executing commands issued by the master.
The figure illustrates the setup of the described system.

</p>



<p align="center">

<img src="https://github.com/user-attachments/assets/a66ca8bc-390f-49d9-802b-3b671a5d6c70" alt="Smart_markers" width="70%"/>

</p>

Na osnovu navedenog, softverska komponenta cjelokupnog sistema može podijeliti na tri dijela:

    * računarska aplikacija za upravljanje sistemom aktivnih markera,
   * softver Master uređaja,
    * softver Aktivnih markera.

Zbog bolje sinteze i analize softvera potrebno je opisati proces rada sistema, te njegove funkcionalnosti.
