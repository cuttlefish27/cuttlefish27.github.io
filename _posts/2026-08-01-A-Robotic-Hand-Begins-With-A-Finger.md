---
layout: post
title: A Robotic Hand Begins With a Finger
date: 2026-08-01
---
<script type="module" src="https://unpkg.com/@google/model-viewer/dist/model-viewer.min.js"></script>

Humanoid robotics has always fascinated me, and within that field, few components are as deceptively complex as the human hand. When I set out to design my own robotic finger, I wanted to tackle that complexity head on, starting small with a single finger before working toward something bigger. 

## The Current Design
<model-viewer
    id="finger"
    src="/assets/models/Finger_AssemblyV3.glb"
    camera-controls
    min-camera-orbit="auto auto 20%"
    max-camera-orbit="auto auto 300%"
    environment-image="neutral"
    exposure="0.3"
    shadow-intensity="4"
    shadow-softness="0.5"
    animation-name="ExplodeAction">
</model-viewer>

<style>
model-viewer {
    width: 100%;
    height: 640px;
    background-color: #111;
}
</style>

*Below is a slider to show an exploded view*\

<input 
    type="range" 
    min="0" 
    max="2.5" 
    step="0.01" 
    value="0"
    oninput="scrubAnimation(this.value)">

<script>
const model = document.getElementById("finger");
function scrubAnimation(time) {
    model.pause();
    model.currentTime = time;
}
</script>

Mechanically, the finger has three degrees of freedom. Two SG90 servos work as an opposing tendon pair to handle adduction and abduction, letting the finger point side to side using tension rather than a rigid pivot. A third servo beneath them controls curling. Right now extension is passive, handled by a spring that pulls the finger back after each curl. My early designs used a combination of rubber bands and elastic string, which snapped constantly and never extended with any consistency. Springs solved that issue and gave me repeatable extension and a manageable amount of creep.

On the CAD side, I’ve gone through dozens of iterations per joint, mostly chasing torque and space efficiency simultaneously. Small shifts in pivot points and tendon attachment points had outsized effects on performance. One deliberate choice I made was keeping the servos inside the hand itself rather than routing them back to the forearm, which is common in a lot of other designs. It’s a harder packaging problem, but it keeps the overall footprint smaller and leaves the forearm free for wrist actuation and the electronics.

### Finger Design Iterations
*Version 1*\
<img src="/assets/images/FingerV1-0.JPG" width= "50%">\
*Version 1.5*\
<img src="/assets/images/FingerV1-5.JPG" width= "50%">\
*Version 2*\
<img src="/assets/images/FingerV2-0.jpg" width= "50%">\
*Version 3*\
<img src="/assets/images/FingerV3-0.JPG" width= "50%">\


# The Blender Controller
## Math
My initial versions all relied on potentiometers and switches that directly controlled the actuator and it quickly revealed a fundamental limitation. I could directly change servo positions, but I had no direct way to tell the fingertip where to go in space. That frustration led me to Blender, which I now use as an interactive control interface. I designed a system that takes a Blender pose and copies it on the physical model.

Getting the signal from Blender to the physical hardware required its own pipeline. First an armature in blender is used to pose the finger and additionally uses Blender's built in inverse kinematics solver to solve the joint angles needed to point the finger in a certain direction. Then by deriving the transformation matrix for the finger, I was able to create an expression that represented the change in tendon length as a matrix function of the joint angles. 

### Variable definitions
Let $$p_{1}$$ be the point about which the finger rotates in the sagittal plane.
Let $$p_{2}$$ be the point about which the finger rotates in the frontal plane.
Let $n$ be the offset distance between $p_{1}$ and $p_{2}$
Let $q_{1}$ and $q_{2}$ be the attachment point for the pointing servos in $p_{2}$'s coordinate system
Let $G_{1}$ and $G_{2}$ be the stationary guide holes for the tendons

### Equations

$$
T_{p_1\to B}=
\begin{bmatrix}
1 & 0 & 0 & 0\\
0 & \cos\theta_1 & -\sin\theta_1 & 0\\
0 & \sin\theta_1 & \cos\theta_1 & 0\\
0 & 0 & 0 & 1
\end{bmatrix}
$$
> Transformation from $$p_1$$ to the base

$$
T_{p_2\to p_1}=
\begin{bmatrix}
\cos\theta_2 & 0 & \sin\theta_2 & 0\\
0 & 1 & 0 & 0\\
-\sin\theta_2 & 0 & \cos\theta_2 & n\\
0 & 0 & 0 & 1
\end{bmatrix}
$$

> Transformation from $$p_2$$ to $$p_1$$


$$
T(\theta_1,\theta_2) = T_{p_1\to B} T_{p_2\to p_1}
$$

> Overall transformation

$$
A_1(\theta_1,\theta_2)=T(\theta_1,\theta_2)q_1
$$

$$
A_2(\theta_1,\theta_2)=T(\theta_1,\theta_2)q_2
$$

>Tendon attachment points


Since the distance between the attachment points and the guide holes are the only part of the tendon that is changing, the change in tendon length is equal to the change in distance between those two points.

$$
L_1(\theta_1, \theta_2) = \|G_1 - A_1(\theta_1, \theta_2)\|
$$

$$
L_2(\theta_1, \theta_2) = \|G_2 - A_2(\theta_1, \theta_2)\|
$$

> Above gives us the required tendon lengths, from here on referred to as $L_1$ and $L_2$

$$
\Delta L_1 = L_{1_0} - L_1
$$

$$
\Delta L_2 = L_{2_0} - L_2
$$

> Change in tendon lengths from rest, $$L_{1_0}$$ and $$L_{2_0}$$ are the tendon lengths at rest


## Software
The joint angles calculated in Blender are sent through a socket server to a separate Python script, which uses the above expressions and converts those angles into the tendon lengths needed, then into servo angles. These are formatted into string commands, and sent over serial to an ESP32 that interprets and executes the commands. While the system has many separate, communicating scripts, it still maintains a real-time latency and is reactive to small changes on blender.

# Future plans
Right now, I’m working through a stall torque issue with the curl servo, since it struggles when the finger is fully extended. Once that’s sorted, the plan is to build more fingers, develop a thumb from a few concepts I’ve sketched out, and eventually build toward a full hand with a wrist and forearm. Beyond the engineering challenge, there’s a bigger reason hands specifically drew me in. A well designed robotic hand has a direct path toward better prosthetics, and there are a lot of people who could benefit from better prosthetics.
