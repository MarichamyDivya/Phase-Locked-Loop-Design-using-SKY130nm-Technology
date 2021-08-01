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

For, detecting the phase error between the input reference clock and the feedback clock, we donot use XOR phase detector as it doesnt correct the frequency errors. Instead, we use Phase Frequency Detector , which will correct both, phase and frequency differences between the input reference clock and the feedback clock.

The basic block diagram of PFD is as shown below:

![PFD_block](https://user-images.githubusercontent.com/88256941/127757062-ac847dfe-ae08-4907-a467-977b1c018786.JPG)

When falling edge of reference clock leads feedback clock, the UP signal goes high and DN signal remains low. UP signal remains high until it detects the falling edge of the feedback clock. As soon as feedback clock falling edge arrives, UP signal goes low. 

When falling edge of feedback clock leads reference clock, the DN signal goes high and UP signal remains low. DN signal remains high until it detects the falling edge of the reference clock. As soon as reference clock falling edge arrives, DN signal goes low. 

Dead zone is one of the problem of PFD. The phase difference below which PFD output is not able to reach the desired logical level and fails to turn on the charge pump switches is called the dead zone. Above precision PFDs are present, which overcomes this issues.

2. Charge Pump (CP)

The loop filer takes input as voltage/current. Hence, the pulse width modulates signal at the output of PFD have to be transformed to volateg/current. Therefore, we use charge pump circuit. 

It generates the error current (ICP) which is proportional to the phase difference od pulse width between UP and DN which inturn is proportional to the input phase error. 

  Charge pump chagres or discharges the loop filter based upon the UP and DN signals. If UP signal is high, charge pump charges the loop filter. If DN signal is high, charge pump discharges the loop filter.
  
  Charge pump circuit have of charge leakage, which should be reduced as much as possible.

3. Loop Pass Filter (LPF)

It converts the error current into error voltage. It also controls the loop dynamics and stability of the system. 

For the stability purpose, C2 capacitor should be one-tenth of the C1 capacitor. Loop filter bandwidth should be one-tenth of the highes output frequency.
4. Voltage Controlled Oscillator (VCO)

VCO is the heart of PLL and it generates the desired high frequency clock output. 

The most common on-chip VCO used is the ring oscillators which consists of odd number of inverters. The period will be twice the number of inverter delays multiplied be the inverter delay. One of the way, to control the frequency of the ring oscillator is to vary the current using the current starving technique. It is important to note that, we should design the VCO is such a way that the range of frequencies we want for the PLL is generated properly by the VCO.

5. Frequency Divider (N)

It divides the VCO clock to generate the feedback clock with same frequency as input reference clock.

A toggle flip flop generates the clock which has twice the time period of the input clock given to it, that is we obtain the clock having half the frequency of the input clock provided to the frequency divider. For 8x clock multiplier, we should divide the output clock by 8 to generate feedback clock. For obtaining one-eigth of frequency, we should cascade three toggle flip flops. 

### Important terms used in PLL

1. Lock-in range

Range of freuencies which PLL can stay in lock in steady state. It mainly depends on the range of frequencies VCO can produce and is limited by the dead zone of PFD.

2. Capture range 

Range of frequencies which PLL can lock when started from unlocked condition. Capture range is smaller than the lock-in range. It depends on the loop filter bandwidth.

3. Settling time

The time taken by the PLL to lock from an unlocked state is called the settling time. It depends on how quickly charge pump rises to the stable value.

## Lab setup

We should use the source code to build any software tool as it gives the latest updates and fixes. 

Here, we will use two tools. They are:

Ngspice : for transistor level simulation

Magic : for layout design and parasitic extraction.

### Using tools

From the ubuntu package , we will be downloading Ngspice directly.  Magic we will compile it with the source code because we need the latest version of Magic to support Skywater130nm technology node.

Ngspic: ngsice<circuit_file_name> 

It plots the results based upon the simulation instruction given in the circuit file after simulating the given circuit file. 

Magic: magic - T <Technology_file_from_PDK><the_layout_file_to_open> 

It opens the layout file, where we can view the layout  and make modifications to it. Magic has many features, out of which we will be using parasitic extraction and gds features.

### Developmet Flow

With the PLL specifications, we will perform SPICE-level circuit development and pre-layout simulation. After the pre-layout simulation we develop layout. Before layout phase we donot know the interconnection or proximity or size of the different circuits. After layout, we can extract the capacitive effect which is called a s the parasitic extraction. After this, we have spice netlist which is more closer to the real worls. Then, we do the post simulation which is more accurate than thr pre-layout simulation.

Its is important to note that, after each simulation step, we should make several changes to bring the working of the circuit much closer to the required specification.

### Ngspice setup

We use the following command to isntall Ngspice:

sudo apt-get install ngspice

Then we fetch the sky130 library which consists of the transistor models which is needed for the simulation.
### Magic setup

We will use the source code from opendesigncircuit.com. We can do the git clone and intall the Magic. Then, we should compile it, for that we should install the dependencies form the opendesigncircuit.com. Then we run the ./configure command. The we run the make command. Then we run the command

sudo make install

Then we should install the sky130nm technology information which Magic needs for desiging the layout.

# DAY 2 - PLL simulation (Prelayout and Postlayout)

A spice file is a text file with .cr extenstion.

For example, if we want to write code for frequency divider circuit, we write the following command in terminal:

touch FD.cir

and then page will be created where we should write the code for the desired circuit. Don't forget to include the SPICE library file in it.

Similarly we do it for all the blocks of PLL.

After creating the .cr files we run the Ngspice simulation.

## Pre-layout simulations

### Phase Frequency Detector 


