---
layout: post
title: Magnetic Touch Sensing
date: 2026-10-14
---



As I've continued working on my robotic hand (keep an eye out for the next post), I've noticed a major issue with my current design: it has no way to recieve tactile feedback. But wait, lets take a step back and ask the question "Why is tactile feedback important?"

# Tactile Feedback in Humans

The human hand relies heavily on our sense of touch to manipulate objects safely and precisely. A combination of tactile feedback, force sensing, and propioception are the reason why we can safely hold eggs without crushing them while still having the ability to grab things with force. Another benefit of our tactile sense is the ability to understand the orientation ands tate of objects that are already within our grasp. I know that when we're stuck on a hard problem or just thinking, we often fidget with a pencil or move it around in our hand. I want you to think for a second about the level of control that goes into it. Without looking, we are able to understand the orienation of a pencil just by feeling, and apply just enough force that it won't slip but at the same time can move within our grasp.

These abilities allow the human hand to have the level of precision and dexterity that let us complete complex tasks. This also means that a robotic hand has to find a way to implement these sensing abilities if it hopes to achieve any fraction of human dexterity.

# A Magnetic Approach to Touch Sensing

In this section I present my first design of a magnetic touch sensor. 

## The Scientific Basis

Permanent magnets create a magnetic field around themselves. The strength of this field can be concentrated around objects with high magnetic permeability. By creating a flexible material with a high magnetic permeability, a deformation in the material should produce a measureable change in the magnetic field strength at a point near to the deformation. By using a Hall effect sensor, this change in magnetic field strength can be detected and measured. This process gives me a way to detect when an object touches the sensor surface. 

## The Design
All designs and code are available in the sensor development folder of my [RoboticHandController](https://github.com/cuttlefish27/robotic_hand_controller) repo.

<img src="/assets/images/SensorDesign.JPG" style="display: block; margin: 0 auto; width: 50%:">

The design consists of 3 distinct layers. The top layer is a mix of silicone and iron powder, serving as the flexible material with high magnetic permeability. The idea is that the field will concentrate itself through this layer due to its high iron content. The second layer beneath the silicone is the PCB. This contains the hall sensor and other required circuitry. The final layer is the magnet that produces the magnetic field. 

### Implementation

#### Circuitry

<img src="/assets/images/SensorSchematic.png" width="100%" height="100%">

<img src="/assets/images/PCBLayout.png" width="100%" style="display: block; margin: 0 auto">

The circuit consists of a DRV5056A4 Analog Hall Effect Sensor, a decoupling capacitor, and a low pass RC filter. The hall effect sensor outputs continuous voltage values depending on the magnetic field it detects. Since it is an analog component, a stable input voltage is extremely important for a consistent output. The decoupling capacitor helps stabilize input voltage by acting as a bank of charge that smooths ripples in voltage. The RC filter helps block high frequency noise from the output pin of the sensor giving a more stable sensor reading. 

#### Casing and Silicone Layer
<img src="/assets/images/Casing.JPG" width="80%" style="display: block; margin: 0 auto"> 

The silicone layer was created by mixing iron powder into 2 part silicone and letting the mix cure. This mixture is placed with iron-heavy side directly on top of the PCB. The 3d printed casing surrounds the PCB and silicone and has a place for a small neodynium magnet in the base to serve as the magnetic field source. 

### Data Aquisition and Code structure
<video src="/assets/videos/SensorDemo.mp4" preload="auto" controls muted width="75%" style="display: block; margin: 0 auto"></video>

The sensor is connected to my ESP32 which sends the data through a USB to my computer. The computer has a live graph of sensor readings running using matplotlib, which lets me analyze the sensor's reaction to different stimuli. In my implementation, I have also inluded an updating readout in the terminal which tracks the sensor state, number of presses, threshold value, and current sensor value. In the above video, you can see a demonstration of the sensor's live graph and terminal readout as the sensor is pressed. 

The sensor's output scales with the strength of the press and is able to detect quick taps, long presses, and a wide variety of pressures, from a finger simply resting on it to a strong squeeze. 

## So Why This Design?

While designing this sensor, my  philosophy was to keep the sensor as easy to build and repair as possible. When compared to other tactile sensing mechanisms, this design has numerous benefits. The PCB itself has a small footprint which will help when integrating it into robotic systems. Due to its silicone layer, the sensor itself never comes into contact with the outside world, which protects it from wear and tear. This silicone layer is also made with cheap materials which makes repairs to the silicone surface much simpler than other sensors. Another major benefit is that since the sensing surface is made of a silicone mixture, the sensor shape can be customized by molding the silicone in different ways.

## Future Plans

In the future, I plan to integrate this sensor into my robotic finger design to give it the ability to feel what it is touching. I also hope to add more functionality in the form of force sensing, which would return the force applied on the sensor. Additionally, a design that incorporates multiple hall effect sensors for location sensing is currently in the works. 

Stay tuned for more updates and thanks for reading!