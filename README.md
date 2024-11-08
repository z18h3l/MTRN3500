java c
Assignment 2 - UGV Assignment 
MTRN3500 - UNSW School of Mechanical and Manufacturing Engineering 
October 10, 2024 
Changelog 
• 08/10/24: Initial release
1 Introduction Often, in writing software for Mechatronic Systems, we need to integrate software modules that interact with sensors and actuators and software modules that carry out computational tasks, under the supervision of a thread manage- ment software module.  Rather than building a monolithic application, a multi-threaded approach can be used to separate functional tasks and to simplify the software development, upgrade and maintenance.  Such an approach, alongside appropriate exception handling, has the added benefit of limiting the impact of software errors that may occur during operation, via thread isolation (i.e., if there is an error in one subsystem the rest are able to continue running normally).  As such, we need to use inter-thread communication to allow for these specialised threads to communicate with each other.
2 Learning Outcomes 
A systems integration practical project with an Unmanned Ground Vehicle (UGV)
• To implement object-oriented programs using C++.
• To create a multi-threaded program for concurrent activity.
• To demonstrate inter-thread communication and thread management.
• To interface with and process live streams of data from industrial sensors and to use them to assist in the tele-operation of a UGV.
• To interface with existing software to leverage pre-existing functionality.
Note: The word ‘‘module’’ is used to describe a piece of self-contained software. The word ‘‘thread’’ is used to describe a module that is running.
3 Your task In this assignment, you are expected to write multiple independent modules that interact with each other through shared memory (variables that are shared between independent threads in a program).  Your development should include the following software modules:
•  a Thread Management Module to set up shared memory and to startup, monitor and shutdown the operation of all the modules listed below.
•  a Laser Module to interface to a data stream originating from an LMS151 laser rangefinder and store the data appropriately for access by other threads.
•  a GNSS Module to interface to a data stream originating from a Novatel SMART-VI GNSS receiver and store the data appropriately for access by other threads.
•  a Controller Module to read signals received from an x-box controller and store the data appropriately for access by other threads.
•  a Vehicle Control Module to direct the controller signals to drive and steer the unmanned ground vehicle.
•  a Display Module to graphically display the x-y data of the laser range finder and GNSS data in a virtual world.
•  a Crash Avoidance Module to process in real-time the laser and controller data to avoid collisions of the UGV.You are then required to (i).  demonstrate receipt of Laser and GNSS data, (ii).  demonstrate steering and propulsion control of the UGV and (iii).  demonstrate the graphical display of laser data.  See from Section 3.1 onwards for the details. Start by completing Part I in Section 3.1 and then follow the other parts.
3.1   Part 1 - Thread Management Module The purpose of the thread management module is to set up shared memory, start up all threads and monitor the operational health of all the threads and finally to shutdown all the threads. Develop the thread management module to:
1.  Set up shared memory. The shared memory module must have the capability to:
(a)  Provide read/write access to Thread Management data. (b)  Provide read/write access to Laser data.
(c)  Provide read/write access to GNSS data.
(d)  Provide read/write access to Vehicle Control data.
2.  Start all other threads one by one in the most logical sequence suitable for tele-operation of the UGV.
3.  Monitor the heartbeats of all the other threads.
4.  Carry out a complete shutdown in the event the thread management thread detects the failure of a critically important thread.
5. Attempt to restart a failed non-critical thread.
6.  Carry out a routine shutdown of all the threads in response to pressing the ‘q’ key (within the thread management terminal window).
7.  Indicate to all the other modules that it (the thread management module) is alive and operational.  If thread management fails all other threads should terminate.
Thread Management is the most important module and therefore will form. a critical threads.
3.2 Part 2 - Laser Module The UGV mentioned earlier will have its own server running onboard.  The purpose of the server is to allow you to interact with the UGV. Therefore, in this part your first task is to establish a Wi-Fi connection with this server over the wireless network (you should be able to do this from either your laptop or the lab computers). Use 192.168.1.200 as the IP address and 23000 as the port number to connect to and receive laser data (further information in service details document).  After authentication, you will be allowed to connect to the laser rangefinder to enable you to collect laser range data.  The most recent laser range data you collected must then be stored as an array of X, Y coordinates in the shared memory for other modules to use (an appropriate struct to hold the data is available within SMObjects .h).Incorporate mechanisms in the laser module to update its own heartbeat so that this is observable by the thread management module and for the laser threads to respond to shutdown commands form. the thread management module.
The laser module will form. the primary input for the UGV’s tele-operation and is thus a critical thread.
3.3 Part 3 - GNSS Module Similar to the Laser, the GNSS runs on a server on the weeder thus requiring a Wi-Fi connection to the robot.  The device will run at port 24000 to communicate via a TCP connection to 192.168.1.200. The server does not need any authentication. As soon as you connect to the GNSS receiver it will allow you to get global position data records as a continuous stream of binary data. The arrangement of this binary data can be inspected within the GNSS manual extracts document. Process the binary data to obtain the X, Y position in UTM coordinates and height in meters, and make them available in the shared memory for other modules to use.  In addition, this module should print on the screen, Northing, Easting and Height in meters + the two CRC values (a value that indicates data integrity).
In addition to the CRC recieved in the data packet, to verify the GNSS checksum when data is received, please use the functions provided in Appendix A as a base. You can then compare the CRC sent by the device and that calculated with this code.
Incorporate mechanisms in the GNSS module to enable the thread management module to detect the heartbeat of the GNSS thread and for the GNSS thread to respond to shutdown commands form. the thread management thread.
The GNSS module provides secondary input information to the driver who can continue without this and is thus a non-critical thread.
The GNSS data is simulated on the physical UGV as it is located indoors and as such the northings and eastings both should look like a sinusoidal changing pair of values.
3.4   Part 4 - Controller Module You will use an X-box controller to provide control input to the tele-operation system.  The speed of the robot will be controlled by the right and left triggers (right for forwards motion and left for reverse).  The steering should be controlled by the x-axis of the right joystick. You will be provided with a controller for testing within the laboratory sessions. Make the speed and steering commands generated this way available to the vehicle control module through shared memory.   In addition, program one of the X-box buttons to indicate to the thread management thread to shutdown all modules at the end of your tele-operation.For you to more easily interface with the controller, an interface class has been written. The available data structures and functions are available in the ControllerInterface .h header file.  You should not change this file at all. Note, it is a C++ class (not CLR) when you include it in your project. This will provide you a means of connecting to a controller, getting the state of the controller, checking connection, and other useful functionality. At the top there is a #define defining the type of control input so you can change it between controller input and keyboard input. This can be used so that you are able to test your code at home where you might not have an X-box controller available.
Incorporate mechanisms in the X-box module to enable the thread management thread to detect the heartbeat of the X-box thread and for the X-box thread to respond to shutdown commands form. the thread management thread.
Note: If X-box is disconnected or turned off, set the speed and steering to zero! This forms a critical thread for tele-operation.


3.5   Part 5 - Vehicle Control Module The purpose of the vehicle control module is to send control commands to the UGV. As this is running on a server on the UGV, first establish a Wi-Fi connection with the onboard server to communicate via port 25000 with a TCP connection to 192.168.1.200. After authentication, you must send the UGV commands in the form. of the following ASCII string to make it move.#    #
Replace the field  by a numerical value between ±40◦ as the required steering angle and,  by a numerical value between ±1m/s as the required speed. These values should be based on your control input coming from the controller module (values available via shared memory).  Replace the watchdog field  by 0 and  1 alternatingly to indicate you are actively controlling the vehicle (it should alternate in consecutive messages).  The spaces and the # are required.  The   signal may also be used to inhibit vehicle motion by setting it to be dormant at either 0 or 1. You may find a practical use of this signal when implementing Part 7 described below.Incorporate mechanisms in the vehicle control module to enable the thread management thread to detect the heartbeat of the vehicle control thread and for the vehicle control thread to respond to shutdown commands form. the thread management thread.
The vehicle control module is important in tele-operation thus forms a critical thread.
3.6 Part 6 - Display Module The display module is a means of providing a useful way to display collected sensor information to the user.  Partic- ularly, it should be able to portray the Laser data appropriately (representing the current data).  All data should be plotted relative the the vehicle base frame. To complete this task Matlab will be utilised as a display engine. As such, there will be two components: the module within your Visual Studio codebase and the Matlab software. The latter is provided for you and you do not have t代 写MTRN3500 Assignment 2 - UGV AssignmentC/C++
代做程序编程语言o write this (see provided Matlab files with starter code on Moodle which can be run directly).To interface with the existing Matlab display engine, data must be published to a local TCP/IP connection. It will read this data continuously and plot it appropriately. The format of the message sent should be an array of raw Byte data, sending the Cartesian co-ordinates of the data points.  This should have enough size to fit 361 laser data points.  A code snippet has been provided in Appendix Bto perform. this task of sending the data.
Similar  to  other modules,  the  display module will  require  access  to  the  shared  memory to  collect  data,  modify heartbeats appropriately, and respond to shutdown commands.
The display module provides all the collected information to the user as a first person view, who would otherwise be driving blind, thus forms a critical thread.
3.7   Part 7 - Crash Avoidance Module This module requires you to determine if a crash is likely and if so to inhibit vehicle motion. To make a decision on the impending crash, you need to process the data collected form. both the controller and the laser. A distance to the obstacle of 1 m or less should trigger motion inhibition.For this part of the assignment, marks will be awarded based on the sophistication you will incorporate into your software.  For example, one may decide to activate motion inhibition based on obstacles straight ahead.  Another option is to take the steering signal into account to detect obstacles in the direction the vehicle is steered.  It can be further sophisticated by taking an approximate steered path or in the complete solution the exact steered path.This task will also require you to modify/add to shared memory data structures so new information can be transferred between modules. Additionally, you will need to make sure you understand the format of the laser data in order to appropriately process it.


4 Software Architecture As this software system should be written as object oriented code, there is a specific layout of inheritance that should be followed.  This is provided in the attached diagram, describing the class hierarchy.  Particularly, abstract base classes are provided and each module must appropriately inherit from these and implement the required functions. For further information on working with classes and inheritance review the week 1 lecture.
5 Software Setup The software for this system will exist within a single project within Visual Studio 2022. This means there will be a single main function that launches the first thread (Thread Management module) and from here all others should be created.You have been provided with a Visual Studio solution on GitHub classroom.  Below details the files that are already included in and linked to your project.   Explore it before beginning your assignment (these files form. part of the specification).
Project files: 
For each module, you will need two additional files that will include the implementation of said module.  These will be a header file that will define the class structure and a cpp file that will implement all class functions.
Adding additional files can be done in the solution explorer as below:
• For header files this will be: right click on Header Files, select add->new item, create a header file of appropriate name and type, click add.
• For cpp files this will be: right click on Source Files, select add->new item, create a cpp file of appropriate name and type, click add.
The following table summarises the names that the required files should take.  These should be used EXACTLY in your project.
Module 
Header 
Implementation 
Thread Management 
TMM.h 
TMM.cpp 
Laser 
Laser.h 
Laser.cpp 
GNSS 
GNSS.h 
GNSS.cpp 
Controller 
Controller.h 
Controller.cpp 
Vehicle Control 
VC.h 
VC.cpp 
Display 
Display.h 
Display.cpp 
Crash Avoidance 
CrashAvoidance.h 
CrashAvoidance.cpp 
In addition to these, an implementation for the controller interface has been provided through a compiled library file (ControllerInterface* .lib) that is already linked to your project and ready to use.
Note: To help you get started a template TMM .h and TMM . cpp file has also been provided in the setup repository. 
6 Using the simulator As with the last assignment, you have been provided with a simulator to test your code when outside the lab. It can be installed with the installer provided on Moodle and run in the same way to last assignment’s simulator (extract the files and double click on the executable).To change between running code on the robot and the simulator requires one minor change.  The only difference is the IP address it is running at (all ports and other connection information, including authentication) should be the same.  In the networked_module .h header file, you will need to change WEEDER_ADDRESS to 127.0.0.1 when running on the simulator and 192.168.1.200 when running on the actual UGV (connect to via Wi-Fi in the lab).
If you believe you have found any bugs in it please let us know to review. 
7 Software Considerations 
When writing your software you MUST remember to be following good software development practices.  Particularly, in this assignment you must be:
• Following the prescribed software architecture throughout all modules as outlined in section 4. 
• Performing data validation when reading data from the sensors (Laser and GNSS) to ensure that it has been received correctly/fully and is valid.
• Undertaking appropriate exception handling to ensure safe program execution. This includes:
□ Handling exceptions that you may expect to be thrown by code you have written.
□ Utilising error codes to ensure appropriate code flow.
8 Git Usage As part of this assignment, you should be employing Git to safely store and version manage your software. Similar to last assignment, a repository has been established for you within GitHub classroom. Please follow this link to access the repository. You MUST use the repository set up for you in GitHub classroom.
9   What to look at next? Now that you have read the assignment specification (do not forget to read submission instructions below), you can start setting up your project based on the instructions. There is one additional document (found on Moodle) that you should review. This is the service details document containing important information for connecting to sensors and actuators.Additionally, you should review all the starter code provided, particularly all the header files in the include folder. Note that, there are four files that you will need to interact with directly (SMObjects.h, UGVModule.h, NetworkedModule.h, and ControllerInterface.h).
10 Marking Criteria 
This assignment is worth 25 % of the total course mark. The breakdown of marks for this assignment is described in table 1. 
Table 1: Mark allocation for assignment
Criteria 
Weighting 
Description 
Progress Check 
20 % 
As below in section 10.1 
Final Demonstration 
80 % 
As below in section 10.2 
Important note: None of the marks will be valid if plagiarism is detected in the submitted code or if you did not submit your entire source code by the deadline (see section 12).
All demonstrations of the required functionality can either be completed on your own computers or the lab computers connected to the physical Weeder.
10.1   Progress Check Requirements Complete progress check in week 8. The first progress check is to demonstrate the skeletal layout of all the modules and some base functionality and the operation of the inter-thread communication through heartbeat management. The demonstration required is,1.  Showing the completed class structure for ALL modules. The functions do not have to be implemented (1 mark)
• Header files should be present for all modules.
• All classes should be created in their respective header file, inheriting from the correct location and over- riding the appropriate functions.
2. The Laser thread should be printing live laser range data on the screen. (2 marks)
• The data must be presented as the 361 concurrent data points converted to Cartesian coordinates (will be assessed for correct converted values).
• Data should be printed to the terminal in an easy-to-read fashion as pairs of x and y points.
3. The automatic shutdown of all other modules in response to a routine shutdown of the thread management thread. (2 marks)
• A routine shutdown of the thread management thread is ‘‘pressing the ‘q’ key’’ .
You must get this marked during your allocated lab time in week 8. Late penalties per School policy applies. 
10.2   Final Demonstration Requirements 
Complete the final assessment in week 11 (early submission available as per below).  This is an assessment and therefore there are no repeat opportunities for these tasks. You get only one opportunity to demonstrate.
1.  Plotting laser data on the graphical display. (3 marks)
• Correct plot should update at a reasonable rate with no significant delay from real-time.
2. The operation of the GNSS thread printing live GNSS data to the terminal in a meaningful manner (2 marks)
• Printing should occur continuously (on each receipt of new data) at a reasonable rate.
• Data should include Northings, Eastings, height, recieved CRC (from the sent packet), and calculated CRC.
• Note: GNSS data is simulated due to being indoors and theNorthings and Eastings should form. a sinusoidal path
3.  Demonstrating steering and propulsion of the vehicle in response to controller commands. (3 marks)
• Input should be taken from x-box controller.
• Appropriate inputs should result in correct movement of wheels on UGV.
4.  Demonstrating shutdown of all threads in response to a keyboard command. (1 marks)
• All threads should be individually shut down as well as the thread management module.
• Shutdown button on x-box controller should cause similar shutdown of all threads.
5.  Demonstrating crash avoidance as a combination of laser and VC data (4 marks)
• Items placed in front of the vehicle when underway should cause it to stop immediately.
• Items placed to the side of the laser when turning should have a similar effect.
6. Answering the two questions your demonstrator will ask about your software. (2 marks)
• These will test concepts about the UGV and its sensors as well as the code you have written.
7.  Following prescribed software architecture (2 marks)
8.  Error handling and data validation throughout modules (4 marks)
• Data from sensors has been validated prior to use.
• Exceptions are handled to ensure safe thread execution and error codes used appropriately.
Note: You will not be awarded any marks if your software deviates significantly from the prescribed architecture. 



         
加QQ：99515681  WX：codinghelp  Email: 99515681@qq.com
