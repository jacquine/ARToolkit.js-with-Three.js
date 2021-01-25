# Using ARToolKit.js to make Augmented Reality applications

### Introduction

[ARToolKit.js](https://github.com/artoolkitx/jsartoolkit5) is an emscripten port of [ARToolKit](https://github.com/artoolkitx/artoolkit5) in Javascript. ARToolKit is an augmented reality (AR) library authored in C/C++ that allows programmers to easily develop AR applications.

According to Wikipedia, AR is an **interactive** experience of a real-world environment where the objects in the real world are enhanced by computer-generated perceptual information, sometimes across multiple senses such as sight, sound, smell, and touch. However, most of our interactions of AR applications that are widely used today are from the users and not the application. 

AR is the overlay of virtual computer graphics images on the real world. One of the most difficult parts of developing an AR app is precisely calculating the user's viewpoint in real time so that the virtual images are aligned with the real world. 

### The Basics
In short, it works by tracking "special images" in your video. These "special images" are AR markers and ARToolKit can figure out where there are and what direction they are pointing at. AR markers are just images used by ARToolKit to track the 3D positions of objects in the video and images.

We need three things to build our AR app: 
1. an AR marker,
2. a video (it could be your device's camera, or a video/image), and
3. a way to draw 3D graphics on the video. (in our case, it's three.js!) 


### First: The Different Types of AR Markers

#### Multi-marker


### Second: Video 
    arController.getUserMedia(options)
    arController.getUserMediaThreeScene()
### Third: 



