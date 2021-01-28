# Using ARToolKit.js to make AR web apps with Three.js

## Introduction

[ARToolKit.js](https://github.com/artoolkitx/jsartoolkit5) is an emscripten port of [ARToolKit](https://github.com/artoolkitx/artoolkit5) in Javascript. ARToolKit is an open-sourced augmented reality (AR) library authored in C/C++ that allows programmers to easily develop AR applications.

According to Wikipedia, AR is an **interactive** experience of a real-world environment where the objects in the real world are enhanced by computer-generated perceptual information, sometimes across multiple senses such as sight, sound, smell, and touch. However, most of our interactions of AR applications that are widely used today are from the users and not the application. 

AR is the overlay of virtual computer graphics images on the real environment, usually to enrich visual information or enhance visibility to users. One of the most difficult parts of developing an AR app is precisely calculating the user's viewpoint in real time so that the virtual images are aligned with the real world. 

## The Basics
In short, it works by tracking "special images" in your video. These "special images" are AR markers are nothing more than just images, or visual cues that ARToolKit can use to figure out where they are (in the video) and what direction they are pointing. By getting positions and orientations of the AR markers from ARToolKit, we can draw 3D objects on top of the video at the right places.

Fundamentally, we need three things to build our AR app:
1. an AR marker,
2. a video (it could be your device's camera, or a video/image), and
3. a way to draw 3D graphics on the video. (in our case, it's three.js!)

### 1. (The Different Types of) AR Markers
ARToolKit comes with support for multiple kinds of markers. Note that the markers that ARToolKit can track are flat images.

#### :black_square_button: Square Pattern markers
These markers are square images with a thick black border, usually printed on white background. 

If you observed other AR markers or tried making your own, you might observe that we need to have an image that is 
a) high-contrast and 
b) rotationally asymmetric (if you put it flat on your table and rotate it 90, 180, or 270 degrees, it should look unique in each angle).

The reason that we need them to be high-contrast is because ARToolKit converts these images into 16x16 black-and-white [thresholded images](https://en.wikipedia.org/wiki/Thresholding_(image_processing)#Definition). ARToolKit will first search for a black square (border), and then examine the image to determine if the image matches our registered markers. 


#### :black_square_button: Square Barcode markers
Barcode markers encode a number on a black-and-white marker using binary code. They don't require pre-registering and use little CPU. Think of them as low-res QR codes.
They work like pattern markers: ARToolKit reads thresholded image data from the marker, converts into binary, and converts the bits into a number. 
These are easier to use than pattern markers, all you need is to set the `arController`'s pattern detection mode to one of the barcode detection modes and check the idMatrix attribute of the marker object.


#### :black_square_button: Mixed-mode tracking
With mixed-mode tracking, we can track both pattern and barcode markers. There is slightly more room for error because some pattern markers can be mistaken for barcode markers.


#### :black_square_button: Multi-marker tracking
This is a combination of square image markers and barcode markers. With `loadMultiMarker`, we can have several markers printed on a single flat surface, which lets you track the surface even if some of the markers are not visible. 
Another advantage is that you can put small markers around a non-marker content and for the non-marker to behave like a marker. For example, if we have a map printed on a piece of paper and have small markers printed around it, you can place your AR content on top of the map and it'll work as long as any of the multimarker images are visible.

Some of the benefits of multimarkers include: 
* Increased robustness to occlusion: even when one marker is obscured, the virtual content is still visible.
* Improved pose-estimation accuracy: all marker corners are used to calculate the pose, meaning that the markers effectively cover a larger optical angle, resulting in reduced numerical error.

These are the same advantages 

### 2. Video 
This can be a video or an image. 
In our case, let's get from our device camera using `getUserMedia` API to get an URL for the device camera, and use this URL as the source for our video element. 

```javascript
// arController.getUserMedia(options)

// in this example, we'll use .getUserMediaThreeScene()
window.ARThreeOnLoad = function() {
    arController.getUserMediaThreeScene({
        maxARVideoSize: 640,
        cameraParam: "/camera_para.dat",
    }
}
```

`camera_para.dat` is a camera parameter file with the default camera properties. This file is read each time our application is started. This parameter should be sufficient for a wide range of different cameras, but you can read more about [camera calibration tecniques](http://www.hitl.washington.edu/artoolkit/documentation/usercalibration.htm) in this ARToolKit documentation.

Now we have a video element that shows the device camera video feed on it. 
The `onSuccess` callback in the `options` object gets called with a ready-to-use video element. 
   
   
### 3. 3D graphics
Three.js is a lightweight cross-browser Javascript library/API used to create and display animated 3D computer graphics on a Web browser. Three.js scripts may be used in conjunction with the HTML5 canvas element, SVG or WebGL. 


## :heavy_plus_sign: To load JSARToolKit and Three.js, first include these two minified scripts into your webpage:
```javascript
<script src="build/artoolkit.min.js"></script>
<script src="js/artoolkit.three.js"></script>
```


## Adding a `getMarker` event listener
Now that we have the markers and the video set up, we're ready to start **tracking the markers** into the video. 

We do this by adding an event listener to listen to `getMarker` events on the `arController`. 
Whenever the `arController` detects a marker, it fires a `getMarker` event with the marker details. 

```javascript
arController.addEventListener("getMarker", function(ev) {
    console.log("detected marker with marker id: ", ev.data.marker.id);
}
```

## loading the markers and applying marker positions to `markerRoot`
With the **marker positions**, we can apply these to a [Three.js object](https://threejs.org/docs/#api/en/core/Object3D.matrix) we call `markerRoot`.
Then we add it to `arScene `.

```javascript
// create a sphere in Three.js
var sphere = new THREE.Mesh(
    new THREE.SphereGeometry(1, 8, 8),
    new THREE.MeshNormalMaterial()
);

// loads a single pattern marker
arController.loadMarker("/patterns/your-marker.patt", function(markerId) {

    var markerRoot = arController.createThreeMarker(markerId);
    
    markerRoot.add(sphere);
    
    arScene.scene.add(markerRoot);;;;;;;
    
}

```


## Example 


## References
* [ARToolKit website: hitl.washington.edu/artoolkit/](http://www.hitl.washington.edu/artoolkit/)


