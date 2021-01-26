# Using ARToolKit.js to make Augmented Reality web apps with Three.js

### Introduction

[ARToolKit.js](https://github.com/artoolkitx/jsartoolkit5) is an emscripten port of [ARToolKit](https://github.com/artoolkitx/artoolkit5) in Javascript. ARToolKit is an augmented reality (AR) library authored in C/C++ that allows programmers to easily develop AR applications.

According to Wikipedia, AR is an **interactive** experience of a real-world environment where the objects in the real world are enhanced by computer-generated perceptual information, sometimes across multiple senses such as sight, sound, smell, and touch. However, most of our interactions of AR applications that are widely used today are from the users and not the application. 

AR is the overlay of virtual computer graphics images on the real world. One of the most difficult parts of developing an AR app is precisely calculating the user's viewpoint in real time so that the virtual images are aligned with the real world. 

### The Basics
In short, it works by tracking "special images" in your video. These "special images" are AR markers are nothing more than just images, or visual cues that ARToolKit can use to figure out where they are (in the video) and what direction they are pointing. By getting positions and orientations of the AR markers from ARToolKit, we can draw 3D objects on top of the video at the right places.

Fundamentally, we need three things to build our AR app:
1. an AR marker,
2. a video (it could be your device's camera, or a video/image), and
3. a way to draw 3D graphics on the video. (in our case, it's three.js!)

### 1. (The Different Types of) AR Markers
ARTK.js comes with support for multiple kinds of markers. Note that the markers that ARTK can track are flat images.

#### Pattern markers
These markers are square images with a thick black border surrounded by a thick white border.
If you observed other AR markers or tried making your own, you might remember that we need to have an image that is a) high-contrast and b) rotationally asymmetric (if you put it flat on your table and rotate it 90, 180, or 270 degrees, it should look different in each angle).

The reason that we need them to be high-contrast is because ARTK converts these markers into a 16x16 black-and-white thresholded image, and recognizes the rectangle shapes in that image. After finding the pattern (rectangular shapes) it compares them to registered markers. If enough pixels match a registered marker, ARTK will tell our app that it has found a marker and calculates the transformation matrix that would turn a square into 

Code snippet that initializes ARTK and loads a pattern marker: 
```javascript
var video = document.querySelector('video');
var ar = new arController(video.videoWidth, video.videoHeight, 'Data/camera_para.dat');

```

#### Barcode markers
Barcode markers encode a number on a black-and-white marker using binary code. They don't require pre-registering and use little CPU. Think of them as low-res QR codes.
They work like pattern markers: ARTK reads thresholded image data from the marker, converts into binary, and converts the bits into a number. 
These are easier to use than pattern markers, all you need is to set the `arController`'s pattern detection mode to one of the barcode detection modes and check the idMatrix attribute of the marker object. 
```javascript
arController.setPatternDetectionMode( artoolkit.AR_MATRIX_CODE_DETECTION);
arController.addEventListener('getMarker', function(ev) {
    console.log('saw barcode marker', ev.data.marker.idMatrix);
}
```

#### Mixed-mode tracking
With mixed-mode tracking, we can track both pattern and barcode markers. There is slightly more room for error because some pattern markers can be mistaken for barcode markers. 
```javascript
arController.setPatternDetectionMode( artoolkit.AR_TEMPLATE_MATCHING_COLOR_AND_MATRIX );
arController.addEventListener('getMarker', function(ev) {
    
}
```


#### NFT markers


#### Multi-marker tracking
This is a combination of square image markers and barcode markers. With `loadMultiMarker`, we can have several markers printed on a single flat surface, which lets you track the surface even if some of the markers are not visible. 
Another advantage is that you can put small markers around a non-marker content and for the non-marker to behave like a marker. For example, if we have a map printed on a piece of paper and have small markers printed around it, you can place your AR content on top of the map and it'll work as long as any of the multimarker images are visible.
```javascript
//load multiMarker
arController.loadMultiMarker('patterns/multi-file.dat', function(markerId, markerNum) {
    ...
}

```
#### Which marker to use?
Choosing which type of marker to use depends on your requirements. If you want fast tracking and have smaller 3D assets, go with the square pattern markers. Use multimarkers for more robust tracking, and to render bigger 3D assets.

### 2. Video 
This can be a video or image. 
```javascript
arController.getUserMedia(options)
arController.getUserMediaThreeScene(...)
```
    
### 3. 3D graphics
Three.js is a lightweight cross-browser Javascript library/API used to create and display animated 3D computer graphics on a Web browser. Three.js scripts may be used in conjunction with the HTML5 canvas element, SVG or WebGL. 

### To load JSARToolKit and Three.js, include these two minified scripts into your webpage
```javascript
<script src="build/artoolkit.min.js"></script>
<script src="js/artoolkit.three.js"></script>
```

### Getting marker positions and identifying different markers
Now that we have the markers, and the video set up, we're ready to start **tracking the markers** into the video. When the camera sees a marker, we want to know a) which marker it is, and b) its details, where it is (position, rotation, scale etc.) 

We do this by adding an event listener to listen to `getMarker` events on the arController. 
Whenever the arController detects a marker, it fires a `getMarker` event with the marker details. 

```javascript
arController.addEventListener('getMarker', function(ev) {
    console.log("getMarker", ev.data.marker.id);
}



```

### Three.js
Now that we have the **marker positions**, we can copy these into a [Three.js object] (https://threejs.org/docs/#api/en/core/Object3D.matrix). 
```javascript
// markerRoot is a THREE.Object3D that tracks the marker position
var markerRoot = arController.createThreeMultiMarker(markerId);

// add markerRoot to the AR scene
arScene.scene.add(markerRoot);

```



### References


