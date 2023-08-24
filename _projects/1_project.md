---
layout: page
title: Auto-Piano Based On Audio Detect
description: A piano based on Raspberry Pi that can identify music and play it with the piano.
img: assets/img/pj1/12.jpg
importance: 1
category: work
---

<p style="text-align:justify; text-justify:inter-ideograph;">
This project build an auto-piano based on Raspberry 4bi and other peripherals, whcih can identify the external playing music and play it with the piano. The whole project can be divided into software part and hardware part.
</p>
## <font color="#dd0000">Software Part</font><br /> 
In the software part, we base on the library in *python* named *librosa* to process the audio signals. The whole process consists of the following four steps.

### Spectral Observation

We first use the functions in *librosa* to extract the frequency, power and CQT(Constant Q-transform, a very useful transformation for audio signal, see the [Wikipedia](https://en.wikipedia.org/wiki/Constant-Q_transform)) spectral of the audio signal and observe them. Just look at the following picture.

<div class="row" align=center>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/pj1/5.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    From top to bottom, the picture shows the frequency, CQT and power spectral respectively.
</div>

### Sampling and Pre-processing
<p style="text-align:justify; text-justify:inter-ideograph;">
We use the following code to get the fundamental frequecy of each audio frame.
</p>
```
y, sr = librosa.load('./demo1.wav') # Read the .wav audio file and its sampling rate
x = np.abs(librosa.stft(y)) # Conduct short time Fourier transform of the audio
xx = librosa.power_to_db(x, ref=np.median) # Use the median to convert the power to decibel
f0 = librosa.yin(y, fmin=262, fmax=1967) # Use the Yin algorithm to estimate the fundamental frequency per frame.
```
### Audio Processing

In practical playing and recorded music, their exists "Rest" between different notes, which brings much interference to the identification. To tackle this issue, we process the decibel matrix to get the mean decibel of each frequency component, thus we can set some thresholds to determine the note.

In the process, we use the ```librosa.hz_to_note()``` function to map the frequency to the note. We set a threshold of decibel to determine whether we receive an audio signal, and another threshold of time to determine whether to view the signal as a new note.

### Output
<p style="text-align:justify; text-justify:inter-ideograph;">
We map the note information to discrete number with the following matrix, such that it can be transmitted to the I/O port to drive the motor.
</p>
```
mapp = {'C3':'-7','C♯3':'-7','D3':'-6','D♯3':'-6','E3':'-5','F3':'-4'
 'F♯3':'-4','G3':'-3','G♯3':'-3','A3':'-2','A♯3':'-2','B3':'-1','C4':'1',
 'C♯4':'1','D4':'2','D♯4':'2','E4':'3','F4':'4','F♯4':'4','G4':'5',
 'G♯4':'5','A4':'6','A♯4':'6','B4':'7','C5':'8','C♯5':'8','D5':'9',
 'D♯5':'9','E5':'10','F5':'11','F♯5':'11','G5':'12','G♯5':'12','A5':'13',
 'A♯5':'13','B5':'14','C6':'15','C♯6':'15','D6':'16','D♯6':'16',
 'E6':'17','F6':'18','F♯6':'18','G6':'19','G♯6':'19','A6':'20',
 'A♯6':'20','B6':'21',}
```
Finally, we get the following list, which refers to the note and the duration respectively.
<div>
    <div class="col-sm mt-3 mt-md-0" align=center>
        {% include figure.html path="assets/img/pj1/1.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
```
-2 500
6 250
6 1000
1 250
1 500
5 250
5 1000
1 250
0 1000
```
<div class="caption">
The score and corresponding I/O information.
</div>

## <font color="#dd0000">Hardware Part</font><br /> 

### System Architecture
<div class="row justify-content-sm-center" align=center>
    <div class="col-sm-8 mt-3 mt-md-0" align=center>
        {% include figure.html path="assets/img/pj1/12.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
            <div class="caption" align=center>
        Components of the hardware part
    </div>
        <div>
        The hardwar part includes five components: a Raspberry 4bi, a PCA 9685 PWM control Board, multiple SG90 servo motors, a simple piano and some pieces of wood for support. 
        </div>
    </div>
    <div class="col-sm-4 mt-3 mt-md-0" align=center>
        {% include figure.html path="assets/img//pj1/11.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
            <div class="caption" align=center>
        The block diagram of PCA9685
    </div>
    </div>
</div>

### Control Method

1. The program transmits the note and duration in the software part to the I/O function.
2. As shown in the above figure, PCA9685 adopt $$I^2C$$ protocol to communicate with Raspberry 4bi. The I/O function use the library of PCA9685 to find the address of the connected servo motor. By configuring the duty cycle of PWM siganl, we can rotate the servo motor. The duration time can be controled by the function ```time.sleep()```.
3. Multiple triggers are deployed on the motor, which can be used to control the keyboard.

### Display
<p style="text-align:justify; text-justify:inter-ideograph;">
To ensure pureness and accuracy of the signal, all the audio are recorded by the *Garageband* of *Apple Inc.*.
</p>
<div class="col-sm mt-3 mt-md-0" align=center>
    {% include video.html path="https://www.youtube.com/embed/CYFEj38LTRg" class="img-fluid rounded z-depth-1" %}
</div>

## <font color="#dd0000">Comment</font><br /> 
<p style="text-align:justify; text-justify:inter-ideograph;">
1. Shortcomings: This project can only accurately identify very simple music, while practically much music tends to have complex rhymes, like liaison, syncopation and dots. For example, this project may take multiple notes with very short intervals as one whole note.
2. Beyond simple spectral analysis of audio signal, to capture more features, actually it is possible for us to use machine learning method to process the sepctral information. For instance, we can build classifiers for tone and rhyme, respectively.
</p>