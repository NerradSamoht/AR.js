- redo parameters-tuning.html
  - all parameters exposed as button - stored as json in url
  - to replace the demo.html in a-frame - which is super broken anyway
  - 3 groups of parameters : source, context, controls. make it 3 group on screen too


# Multimarker

- hide part of the markers
- i should be able to build my own markers
  - what is the good API for that


- find a working version - git checkout 7cc3bedc54fdb4fe9f92138792c36164a3c5c81a
  - and slowly come back to understand why it doesnt work
  - https://github.com/artoolkit/jsartoolkit5/issues/34
  - work on old compile of .min.js - what changed in the new version ? is that the same artoolkit version ? is that the new compilation ?
- generation of multimarker file on the fly and load it in - blob url
  - http://stackoverflow.com/questions/30864573/what-is-blob-url-why-it-is-used-explain
- multiple-indepandent-markers in three.js
- take a picture with kanji and hiro
  - recognize them independantly
  - build a multimarker file with them

### PS vita workflow
- perfect for desk augmented reality
- PS vita workflow : the user put the card on the table, the way it likes
  then we locate each marker and build a multimarker description file
  we start to track this multimarker.

- start printing barcode markers.
  - each is a number barcode - 1 2 3 4 5
- better would be to actually use ps vita card
  - find out which barcode it is
  - try each possible barcode encoding
- just take known barcode images from artoolkit repository
  - https://github.com/artoolkit/artoolkit5/tree/master/doc/patterns/Matrix%20code%203x3%20(72dpi)
  - print them in a pdf
  - google draw


# Misc
- add show/hide into arcode.html url
  - thus the apps workflow is finished
- moving three.js/ at the root ?  
- for refraction, do some deforming mirror effect
  - put dat.gui in it
  - various shape which will act as mirrors
  - cylinder with shrinked middle, dilated middle
  - animated geometry ?
- do a jelwery examples
  - get a model or ring, one model of diamond
  - https://www.transpacific-software.com/webgl/ring.html?tPage=7
  - https://www.shadertoy.com/view/ltfXDM
  - a refraction
  - one ruby, one gold
- add fresnel with buble - even with fake envmap
  - http://filmicworlds.com/blog/everything-has-fresnel/
  - http://kylehalladay.com/blog/tutorial/2014/02/18/Fresnel-Shaders-From-The-Ground-Up.html
  - http://www.tingting-wu.com/webgl_projects/ttwu_shaders.html
- see about an example of videoinwebgl + vreffect

- DONE re-integrate dead reckoning
  - rename motion prediction into deadreckoningcontrols - more precise


- DONE put THREEx.ArToolkitContext.baseURL = '../' in all demo
- DONE add fish in pool hole-in-the-wall
  - https://blog.int3ractive.com/2012/05/fish-boids-threejs-demo.html
- DONE put liquid marker as a single html
  - or a directory, no need to be dirty
- DONE liquid-table.html
  - video texture + animation of sin 
  - center of finger click is the center of the wave
  - it can be water wave
  - it can be fluild real 
  - integrate physics from real finger
  - it can be like the finger in matrix - https://www.youtube.com/watch?v=b2MSF35IxVE&feature=youtu.be&t=98
  - http://mrdoob.com/lab/javascript/webgl/voxels_liquid/index.html

  
# Multimarker
- how to say 'those markers will start act as one'
- actually print 6 cards with matrix markers on simple papers, and play with them
- you need to ensure this is working as a setup
- multipattern seems to work on old version... not on new version
  - git checkout 8510249f1d3c20d24bcce2350b95b67c28495971
  - make it work on new version too
- first integrate the multi pattern in ar.js
- then do it dynamically
- multiple pattern
- multiple matrix - this one seems bogus even on a old one
- algo: 
  1. the user put all the marker on the table
  2. this is deemed the stable positional
  3. from all those positions, build a Multimarker
  4. init artoolkit with this multimarker
- this is all about programmation, no subtle algo
- see https://github.com/artoolkit/jsartoolkit5/issues/34

# webvr-polyfill
- GOAL: works well using only the positional tracking, not the stereo display
  - thus it works well with all three.js examples
- handle resize - currently the canvas isnt using the css it should
  - canvas is sent to the webvr with .requestPresent(layer)
- webvr polyfill to present in single screen - like smus/webvr-polyfill
  - look at his tuning and do the same
  - just do the call on the webvr-polyfill and see his framedata and all
- issue with the projection matrix being inverse in y and z
- LATER: make it work with a-frame

# Profile
- do a threex-artoolkitprofile.js with various performance profile
  - var arToolKitProfile = new THREEx.ARToolKitProfile(type)
  - may be dynamic - for resolution - 'dynamic'
  - type = 'phoneInHand' || 'desktop'
  - thus the user can go a in profiler.html and try various profiles until he find the one he needs
  - then we store that in a cookie, and other applications all use this profile
  - cookie/localstorage makes it stored on the browser, need one profiler per domain tho
  - but no database and no authentication needed
- DONE artoolkit-profile.html to store the profile in localstorage
  - it allows you to select which profile you like
  - it has a <select> and store it in the storage - desktop-normal - phone-normal - phone-slow - dynamic
  - in ctor, if there is a local storage use this

# TODO
- DONE fix projection camera which inversing y axis, and looking toward positive z
  - this affect webvr polyfill in three.js demo
- currently the source image ratio is always in 640x480 :(
  - the aspect of the webcam should depends on the screens
  - it will improve the accuracy of the marker detection. trackable from further away

# Idea about performance - js profiling
- do it on canary. this is the most advanced tool for that
  - POST: optimising AR.js with chromedevtools
- more than 70% of the time is used to copy the image in the HEAP
  - .drawImage, .getImageData
  - this.dataHeap.set( data ) is 43% of the total
  - if i can send just a pointer on the data... i gain 43% in one shot
- performance remove copy to heap
  - http://kapadia.github.io/emscripten/2013/09/13/emscripten-pointers-and-pointers.html
  - this explains how to pass a pointer from a typearray to c++ 
  - this would avoid the dataHeap.set() - 43%
