---
layout: distill
title: Horus
description: Blind-area Detection and Communication based on Reconfigurable Intelligent Surface
img: assets/img/horus/2.gif
importance: 1
category: research
authors:
  - name: Qinpei Luo
    affiliations:
      name: Peking University
bibliography: horus.bib
toc:
  - name: I. Introduction
  - name: II. Methodology
    subsections:
      - name: 1. Frequency Shift Keying Radar
      - name: 2. Frequency Shift Keying Modulation
---
## I. Introduction

According to<d-cite key="najm2007pre"></d-cite>, nearly a quarter of all traffic accidents that occur in the United States result from the collision at the intersection. The obstructed view due to other vehicles, buildings and other possible sources is the main reason and accidents can occur when the sensors deployed on the vehicle (Lidar and Radar) fail to perceive other vehicles, pedestrians, or obstacles in these blind spots. Besides, in both indoor and outdoor scenarios, autonomous cars or robots face the same problem of limited view of their sensors.

<div class="row" align=center>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/horus/sys1.gif" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
  <div class="col-sm mt-3 mt-md-0">
      {% include video.html path="assets/video/horus/horus1.mp4" class="img-fluid rounded z-depth-1" controls=true autoplay=true %}
  </div>
</div>
<div class="caption">
    Potential risk of collision due to the limited coverage of Lidar/Radar in blind areas
</div>

To tackle the above issue, we propose *Horus*, an integrated sensing and communication (ISAC) system based on reconfigurable intelligent surface (RIS), which has the ability to detect and track moving objects in a wide range of blind areas. Besides, with carefully designed ISAC protocol, *Horus* can transmit alarm information to the vehicle to avoid possible collision in advance compared to the method based on vehicle-deployed Lidar and Radar.

<div class="col-sm mt-3 mt-md-0" align=center>
    {% include figure.html path="assets/img/horus/sys2.gif" title="example image" class="img-fluid rounded z-depth-1"%}
</div>
<div class="caption">
    Illustration for the work procedure of Horus
</div>

## II. Methodology

### <font size="5pt">1. Frequency Shift Keying Radar</font><br /> 

Radar systems are traditionally associated with the transmission and reception of radio frequency (RF) signals for detecting and tracking objects. FSK, in the context of radar, involves changing the frequency of the transmitted signal to convey information, which is often carried by the phase of signals.

Let's start from the simplest problem, if the baseband frequency is $f_{c1}$, then the transmitted signal can be expressed by,
\begin{equation}
S_{tx}=A_{tx}e^{j(2\pi f_{c1}t+\phi_1)},
\end{equation}
where $A_{tx}$ represents the gain of the transmit antenna, and $\phi_1$ denotes the initial phase shifts introduced by the phase-lock-loop.

<div class="col-sm mt-3 mt-md-0" align=center>
    {% include figure.html path="assets/img/horus/fsk_radar_flow.jpg" title="example image" class="img-fluid rounded z-depth-1"%}
</div>

The distance between the transmit antenna and RIS can be omitted compared to the range of the object. Thus, the distance of flight can be directly expressed by $2R$. To measure the angle of the detected object, we can place two receive antennas with a very small interval $d$, and the received signals can be expressed by
\begin{equation}
S_{rx1}=A_{rx1}e^{j(2\pi f_{c1}t+\phi_1-\frac{4\pi f_{c1}R}{c})}
\end{equation}
and
\begin{equation}
S_{rx2}=A_{rx2}e^{j(2\pi f_{c1}t+\phi_1-\frac{4\pi f_{c1}(R+dsin\theta)}{c})},
\end{equation}
in which $A_{rx1}, A_{rx2}$ represents the gain of two paths considering both the antenna gain and propagation loss. Here $\theta$ represents the azimuth angle of the object relevant to the antenna, and by the operation of multiplying conjugate, one can determine the value of it.
Take the received signal $S_{rx1}$ and multiply it with the conjugate of the transmit signal, we have
\begin{equation}
S_{IF}=A_{rx1}A_{tx}e^{-j\frac{4\pi f_{c1}R}{c}}=Ae^{j\psi_1}, \psi_1=-\frac{4\pi f_{c1}R}{c}.
\end{equation}

One may say that with the phase information of a single-frequency wave, we can already know the range of the object. However, it is impossible in practice as it is a continuous wave and we can not locate where to start measurement. Besides, to get higher accuracy of ranging, we often choose to use frequency up to GHz, in that case, the maximum range of $\frac{c}{2f_{c1}}$ is too small to satisfy basic requirements.

To tackle this issue, we introduce another transmit signal with a different frequency $f_{c2}$, and follow the same procedure to get the phase information $\psi_2$, then combining the two received signals we have,

\begin{equation}
R=\frac{c(\psi_2-\psi_1)}{4\pi(f_{c1}-f_{c_2})}.
\end{equation}

FSK radar can exhibit resilience to certain types of interference due to its inherent modulation scheme<d-cite key="wang2019spectrum"></d-cite>. It can be considered a form of spread-spectrum communication. Spread spectrum techniques involve spreading the signal energy over a wide frequency band, which can make the radar system less susceptible to narrowband interference. And by averaging multiple measurements with it, the effects of interference, which may be random or sporadic, can be reduced. This can improve the overall robustness of the radar system.

Though FSK radar has many advantages concerning the aspect of radar, we choose this method mainly for another important reason, which will be discussed in the next subsection.

### <font size="5pt">2. Frequency Shift Keying Modulation</font><br />

In the last subsection, the information carried by the phase of FSK signals is well-discussed, which can be used to determine the range/angle of the object. However, apart from the phase, the amplitude of FSK signals also carries information. In traditional FSK modulation and demodulation<d-cite key="1075546"></d-cite>, it follows a pattern of time division, i.e., at one time slot the transmitter emits the frequency of $f_{c1}$ while at another time slot, it emits $f_{c2}$. This pattern is somehow unavailable for us to measure the range because we need to emit signals with two frequencies simultaneously. 

<div class="col-sm mt-3 mt-md-0" align=center>
    {% include figure.html path="assets/img/horus/FSK.png" title="example image" class="img-fluid rounded z-depth-1"%}
</div>
<div class="caption">
Illustration for Frequency-shift keying from <a href="https://en.wikipedia.org/wiki/Frequency-shift_keying">Wikipedia</a>
</div>

To achieve ISAC without transmitting different signals in different time slots. We choose to simultaneously transmit signals with two frequencies but with different amplitudes. For example, if we want to transmit the bit "1", we set the amplitude of the signal with frequency $f_{c1}$ as 1 and the signal with frequency $f_{c2}$ as 0.1, while for bit "0" is vice versa. The aggregate signal will be transmitted, the radar receiver will still be able to identify the phase difference to get the location of the object, while the receiver deployed on the vehicle can just follow the basic FSK demodulation scheme to get the information transmitted from *Horus*.

<div class="row" align=center>
<div class="col-sm mt-3 mt-md-0" align=center>
    {% include figure.html path="assets/img/horus/fsk2.png" title="example image" class="img-fluid rounded z-depth-1"%}
</div>

<div class="col-sm mt-3 mt-md-0" align=center>
    {% include figure.html path="assets/img/horus/fsk1.png" title="example image" class="img-fluid rounded z-depth-1"%}
</div>

</div>
<div class="caption">
Comparison of two different modulation schemes (Left: Classical FSK; Right: FSK for ISAC in Horus)
</div>

### <font size="5pt">3. Reconfigurable Intelligent Surface</font><br />

As a cutting-edge physical-layer technology for wireless communication, the reconfigurable intelligent surface (RIS) has the potential to further enlarge the capacity of communication systems meanwhile providing larger and more accurate sensing. It consists of numerous passive reflecting elements, which can be metamaterial-based elements or antennas. These elements can be electronically controlled by external voltage or current, thus, it has the ability to change the amplitude and phase shifts of incident signals to create more directional beams. RIS is considered energy-efficient because it operates with passive reflecting elements that do not require continuous power. This can contribute to the overall energy efficiency of wireless communication systems.

<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.html path="assets/img/horus/element.png" title="example image" class="img-fluid rounded z-depth-1"%}
    </div>
    <div class="col-sm-4 mt-3 mt-md-0">
        {% include figure.html path="assets/img/horus/RIS.png" title="example image" class="img-fluid rounded z-depth-1"%}
    </div>
</div>
<div class="caption">
    Overview of RIS (Left: Element Design; Right: Front view)
</div>

