# Phase-Locked-Loop-Design-using-SKY130nm-Technology
Workshop, 31 July 2021 and 1 August 2021

## Overview
The two day workshop on designing Phase Locked Loop (PLL) using SKY130nm Technology comprised of the following things:

Day 1 : Basic theory of PLL and the steps for lab setup
Day 2 : PLL simulation (Prelayout and Postlayout)

# DAY 1 - Basics of PLL and Steps for lab setup

The day 1 covers the basics of PLL and steps to install ngspice and magic for designing of PLL.

## Basics of PLL

### PLL

Phase locked loop is a feedback system which generates a high frequency, low noise clock.

Crystal Oscillators and Voltage controlled oscillators are the two ways to generate the clock signal. 

Crystal oscillators are the purest form of clock with excellent phase noise. The drawback of crystal oscillators is that it has very low frequency and have very limited tuning range. Hence, Voltage controlled oscillators (VCOs) are used which can generate high frequency clock, but it has poor phase noise. To get a low phase noise, high frequency clock, we use phase locked loop which makes the VCO mimic the crystal to achieve low phase noise, while still getting high frequency clock.

Phase noise is the random fluctuation of the zero crossing of a periodic waveform , that is, untimely arrival of data. 

The block diagram of PLL is as shown below:

![Digital PLL](https://user-images.githubusercontent.com/88256941/127756019-f91551d5-92fd-4cfd-afb7-49beeda58476.JPG)

PLL has five blocks: 

1. Phase Frequency Detector (PFD) 

It detects the phase (and frequency) error between the input reference clock IN (that is, crystal oscillator) and feedback clock FB. It generates two output signals, UP and DN, whose pulse width is proportional to the phase error.

2. Charge Pump (CP)

It generates the error current (ICP) which is proportional to the phase difference od pulse width between UP and DN which inturn is proportional to the input phase error. 

3. Loop Pass Filter (LP)

It converts the error current into error voltage. It also controls the loop dynamics and stability of the system.

4. Voltage Controlled Oscillator (VCO)

VCO is the heart of PLL and it generates the desired high frequency clock output. 

5. Feedback Divider (N)

It divides the VCO clock to generate the feedback clock with same frequency as input reference clock.

### Phase Frequency Detector (PFD)

For, detecting the phase error between the input reference clock and the feedback clock, we donot use XOR phase detector as it doesnt correct the frequency errors. Instead, we use Phase Frequency Detector , which will correct both, phase and frequency differences between the input reference clock and the feedback clock.

The basic block diagram of PFD is as shown below:



