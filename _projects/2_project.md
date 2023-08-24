---
layout: page
title: Basys-Robot
description: An auto seek pilot with obstacle avoidance Bluetooth control car based on Digilent Basys3 and Verilog.
img: assets/img/pj2/3.jpg
importance: 2
category: work
---

## <font color="#dd0000">Motivation</font><br /> 

### Background
With the development of artificial intelligence, robots of various types have been gradually entering people's life, which brings much convenience. Inspired by the commercial cleaning robot in people's house, this project uses Digilent Basys3 and some other peripherals to build a *Basys-Robot* based on Verilog HDL.

### Specification
This project has a wide potential range of use cases. It can be applied to people's life to do the housework or search for missed objects. Besides, with additional improvement, it can have more advanced functions to do more complex jobs.

The main functions of the *Basys-Robot* are listed below.
1. Remote Control: It can be controlled remotely with Bluetooth.
2. Tracking: It has the function of visualized identification, thus it can identify a specific object and track it automatically.
3. Self-adaption: During its working process, it can adjust its speed and avoid obstacles.

## <font color="#dd0000"> Hardware Implementation </font><br /> 

<div class="col-sm mt-3 mt-md-0" align=center>
    {% include figure.html path="assets/img/pj2/system.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
</div>
<div class="caption">
    System Architecture of the *Basys-Robot*.
</div>
The whole system can be divided into two modules: transmitter and receiver, which communicates with the protocol of Bluetooth. The transmitter receives the control information from the joystick and transmits it to control the motion of the robot. The receiver simultaneously obtains the information from the transmitter, the ultrasound sensor and the camera. Based on this information, it determines the motion of itself.

<div class="col-sm mt-3 mt-md-0" align=center>
    {% include figure.html path="assets/img/pj2/asmd.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
</div>
<div class="caption">
    Algorithmic State Machine with Datapath (ASMD) chart of the *Basys-Robot*.
</div>

The algorithmic state machine (with datapath) chart is shown above. Once the system enters the state of *START*, it first decides whether the received position of the joystick deviates from the center. If not, it returns to the state of *START*, otherwise, it enters the state of *Transmit*, then transfers to the _CONTROL_ state quickly. It loops until the absence of a _BLOCKED_ signal. Then the system enters the state of *DETECTED*. If it detects the object that needs to be tracked, it moves towards the object, otherwise, it follows the control information from the joystick.

## <font color="#dd0000"> Software Design </font><br /> 

<div class="col-sm mt-3 mt-md-0" align=center>
    {% include figure.html path="assets/img/pj2/joystick.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
</div>
<div class="caption">
    Module of the joystick
</div>

The module of the joystick is shown above, PmodJSTK reads the 20-bit data of the joystick and transmits it to the Bluetooth serial by the register buffer, which receives the position information with a baud rate of 2400. It also adds a 4-bit high pulse signal at the end. As the HC-05 Bluetooth transmits a signal 8-bit one time, thus the 24-bits data passes a converter module before sending it to the Bluetooth.


<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.html path="assets/img/pj2/ctrl1.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-4 mt-3 mt-md-0">
        {% include figure.html path="assets/img/pj2/ctrl2.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Module of the controller
</div>

Once the receiver gets the 8-bit HC-05 Bluetooth data, it reads them into a 20-bit register buffer and a 4-bit read pulse register named *VLD*. If the receiver detects the descending edge of *VLD*, it confirms that the current frame is already read, the data in the register will be output to the x and y controller register.

The x controller determines the directions of the motors to control the rotation of the robot, while the y controller determines the velocity of motor, which is decided by the duty cycle of PWM signal.

<div class="col-sm mt-3 mt-md-0" align=center>
    {% include figure.html path="assets/img/pj2/ultra.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
</div>
<div class="caption">
    Module of ultrasound detection.
</div>

The HC-SR04 Bluetooth module needs a lower frequency to trigger and sample, thus we divide the main clock source to get a 1 MHz clock of this module. The functions of each sub-modules are listed below:
1. *my_trig*: Produce short pulse signal from the clock source.
2. *my_echo*: Analyze the received pulse from the HC-SR04.
3. *my_caclulate*: Calculate the distance of the obstacle.
4. *block_dis*: Decide whether the *Basys-Car* is blocked meanwhile displaying the result to the LED.

<div class="col-sm mt-3 mt-md-0" align=center>
    {% include figure.html path="assets/img/pj2/camera.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
</div>
<div class="caption">
    Module of the object detection.
</div>

The object detection module mainly relies on the communication with ov7670 camera. The core sub-modules include
1. MMCM clock division: We generate two clock sources. The 25MHz clock is used in frame buffer, address generation and VGA signal control and synchronization.
2. VGA: Read 12-bit data from the frame buffer and generate a 3-channel VGA signal with horizontal and vertical synchronization siganl.
3. Ov7670 controller: Communicate with the camera by $$I^2C$$.
4. Camera Capture and Address Generation: According to the resolution rate and sequence of ov7670, it reads 8-bit parallel data and generates the corresponding address, then store them into frame buffer.
5. Frame Buffer: Use the IP core *Simple Dual Port RAM* in *Block Memory* to read and write the data into the RAM.

Once this module gets a full frame of data, it reads from the RAM and outputs it to the VGA, which displays as below:

<div class="col-sm mt-3 mt-md-0" align=center>
    {% include figure.html path="assets/img/pj2/vga.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
</div>
<div class="caption">
    Outputs of the VGA.
</div>

Actually, due to the performance restriction of the ov7670 camera, it has an inferior ability to capture distant objects, accompanied by too much noise and distortion. Thus, we consider identifying close objects, for example, human's hand.

In our test, we find that compared to the background color, when a human's hand appear in front of the camera, the highest bit of b-channel of VGA is low, while the third bit of the r-channel is high. Therefore, we can use these two features to identify human's hand.

However, as the 100MHz clock is too fast, identifying objects instantaneously may lead to larger errors. To do it more accurately, we need to observe the timing graph of ov7670.

<div class="col-sm mt-3 mt-md-0" align=center>
    {% include figure.html path="assets/img/pj2/timing.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
</div>
<div class="caption">
    Timing graph of ov7670.
</div>

As shown in the figure, the data are transmitted with the PCLK clock, when the HREF signal rises, it reads a row of data with 640 bits. Before the two rising edges of VSYNC, there are 480 HREF signals to input each row of data.

Inspired by this, we set a timer with the same PCLK clock once we detect a descending edge of VSYNC. When the next VSYNC is detected, decide whether the timer reach the threshold.

## <font color="#dd0000"> System Result </font><br />

### Resource Usage
<div class="col-sm mt-3 mt-md-0" align=center>
    {% include figure.html path="assets/img/pj2/ctrl.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
</div>
<div class="caption">
    Utilization and Power Consumption of the Controller. We can see that I/O consumes the most power.
</div>

<div class="col-sm mt-3 mt-md-0" align=center>
    {% include figure.html path="assets/img/pj2/robot.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
</div>
<div class="caption">
    Utilization and Power Consumption of the Robot. The power consumption is low as the motors are driven by external power.
</div>

### System Overview

<div class="col-sm mt-3 mt-md-0" align=center>
    {% include figure.html path="assets/img/pj2/ctrl3.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
</div>
<div class="caption">
    Controller Overview
</div>

<div class="col-sm mt-3 mt-md-0" align=center>
    {% include figure.html path="assets/img/pj2/car.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
</div>
<div class="caption">
    Robot Overview
</div>

### Display

<div class="col-sm mt-3 mt-md-0" align=center>
    {% include video.html path="https://www.youtube.com/embed/LO-wC1OpxkY" class="img-fluid rounded z-depth-1" %}
</div>

The whole codework of this project is published on GitHub. See [here](https://github.com/luoqinpei/Auto-seek-Pilot-with-obstacle-avoidance-bluetooth-control-car).