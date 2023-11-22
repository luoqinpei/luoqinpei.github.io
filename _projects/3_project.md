---
layout: page
title: E-Rack
description: A smart clothes hanger.
img: assets/img/pj3/title.jpg
importance: 3
category: course-work
---
## <font color="#dd0000"> Description </font><br />

<p style="text-align:justify; text-justify:inter-ideograph;">
This project is about a smart clothes hanger named E-Rack, which can detect the environment, set the alarm, and analyze data meanwhile being controlled by the user 24/7. It is made for those who spend less time at home, for example, office workers and students. Our proposed E-Rack can detect abnormal weather like rain, snow, sand dust, etc. Then it can send a message to the mobile devices of the user to let them decide whether to retract the hanger. It can also help the user to estimate the remaining time for the clothes to dry.
</p>

## <font color="#dd0000"> System Design </font><br />
<div class="col-sm mt-3 mt-md-0" align=center>
    {% include figure.html path="assets/img/pj3/system.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
</div>
<div class="caption">
    The framework of E-Rack
</div>

<p style="text-align:justify; text-justify:inter-ideograph;">
As shown in the figure, the framework of E-Rack consists of three parts, which are illustrated in detail as follows.
</p>

### Hanger End

<div class="col-sm mt-3 mt-md-0" align=center>
    {% include figure.html path="assets/img/pj3/prototype.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
</div>
<div class="caption">
    The prototype of E-rack
</div>

<p style="text-align:justify; text-justify:inter-ideograph;">
The hanger end includes all the hardware deployed around the rack. It has three sensors that monitor temperature, humidity and raindrops respectively. A servo motor is installed on the end of the hanger to stick out and retract the clothes. All the peripherals are controlled by a central board, which also communicates with the database through the protocol of HTTPS to upload the sensor information and download control information from the mobile application.
</p>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/pj3/hardware1.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/pj3/hardware2.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/pj3/hardware3.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
From left to right, the figure shows (a) Circuit schematic diagram (b) PCB diagram (c) 3D model and the actual circuit.
</div>

### Database & Server Backend

In this project, we choose to use the [Weixin Cloud Base](https://developers.weixin.qq.com/miniprogram/en/dev/wxcloud/basis/getting-started.html) as our database and server. This cloud service not only provides a database to store our data, but also offers functions to interact with our application frontend. 

In the server, we also call a public internet weather API of [Amap](https://lbs.amap.com/) to get the real-time weather data, which is compared with the data from the sensor to get a more accurate analysis. We can also use the weather information to trigger an alarm of weather that cannot be detected by our sensors, like sand dust.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/pj3/api.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/pj3/server.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
Flowgraph of the Internet API and server
</div>

### Application Frontend

The mobile application UI of this project is based on the [Weixin Mini Program](https://mp.weixin.qq.com/cgi-bin/wx?token=&lang=en_US). Users don't have to download extra applications if they've already downloaded *Weixin* on their mobile phones.
<div class="col-sm mt-3 mt-md-0" align=center>
    {% include figure.html path="assets/img/pj3/UI1.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
</div>
<div class="caption">
Mobile Application UI: Enter Page
</div>

<div class="col-sm mt-3 mt-md-0" align=center>
    {% include figure.html path="assets/img/pj3/UI2.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
</div>
<div class="caption">
Mobile Application UI: Main Page
</div>

<div class="col-sm mt-3 mt-md-0" align=center>
    {% include figure.html path="assets/img/pj3/UI2.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
</div>
<div class="caption">
Mobile Application UI: Pop-up Page
</div>

## <font color="#dd0000"> Display </font><br />

<div class="col-sm mt-3 mt-md-0" align=center>
    {% include video.html path="https://www.youtube.com/embed/hHn3wvyz7V0" class="img-fluid rounded z-depth-1" %}
</div>

The whole codework and hardware design files of this project are published on GitHub. See [here](https://github.com/luoqinpei/E-Rack).