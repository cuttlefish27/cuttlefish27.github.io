---
layout: post
title: A Robotic Hand Begins With a Finger
date: 2026-08-01
---
<script type="module" src="https://unpkg.com/@google/model-viewer/dist/model-viewer.min.js"></script>

Humanoid robotics has always fascinated me, and within that field, few components are as deceptively complex as the human hand. When I set out to design my own robotic finger, I wanted to tackle that complexity head on, starting small with a single finger before working toward something bigger. 

# The Design
<model-viewer
    id="finger"
    src="/assets/models/Finger_AssemblyV3.glb"
    camera-controls
    min-camera-orbit="auto auto 0.02m"
    max-camera-orbit="auto auto 2m"
    environment-image="neutral"
    exposure="0.6"
    shadow-intensity="2"
    shadow-softness="1"
    animation-name="ExplodeAction">
</model-viewer>

<style>
model-viewer {
    width: 100%;
    height: 640px;
    background-color: #111;
}
</style>

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

Mechanically, the finger has three degrees of freedom. Two servos work as an opposing tendon pair to handle adduction and abduction, letting the finger point side to side using tension rather than a rigid pivot. A third servo beneath them controls curling. Right now extension is passive, handled by a spring that pulls the finger back after each curl. That spring came after a fairly painful lesson: my early designs used a rubber band and elastic string, which snapped constantly and never extended with any consistency. Springs solved that outright. 




My first version relied on potentiometers for control, and it quickly revealed a fundamental limitation. I could dial in servo positions, but I had no direct way to tell the fingertip where to go in space. I was controlling the puppet strings, not the puppet. That frustration led me to Blender, which I now use as an interactive control interface. I can position a target in the simulation and let Blender’s built in numerical inverse kinematics solver work out the joint angles, sparing me from deriving those equations by hand. 



Getting the signal from Blender to the physical hardware required its own pipeline. The joint angles calculated in Blender are sent through a socket server to a separate Python process, which converts those angles into the tendon lengths needed, then into servo rotations, which are sent over serial to an ESP32 that executes the commands. 

On the CAD side, I’ve gone through dozens of iterations per joint, mostly chasing torque and space efficiency simultaneously. Small shifts in pivot points and tendon attachment points had outsized effects on performance. One deliberate choice I made was keeping the servos inside the hand itself rather than routing them back to the forearm, which is common in a lot of other designs. It’s a harder packaging problem, but it keeps the hand’s footprint smaller and leaves the forearm free for wrist actuation later. 

Right now, I’m working through a stall torque issue with the curl servo, since it struggles when the finger is fully extended. Once that’s sorted, the plan is to build more fingers, develop a thumb from a few concepts I’ve sketched out, and eventually build toward a full hand with a wrist and forearm. Beyond the engineering challenge, there’s a bigger reason hands specifically drew me in. A well designed robotic hand has a direct path toward better prosthetics, and there are a lot of people who could benefit from that. That’s the vision driving where this goes next.
