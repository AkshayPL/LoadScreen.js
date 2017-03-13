# LoadScreen.js
A JS library to handle ThreeJS assets loading and improve UX with a load screen and progress indicator. 
Enjoy waiting.
```js
//create scene, camera, renderer, and insert renderer before

var ls = new LoadScreen( renderer ).onComplete( init ).start( resources );

function init () {
    
    ...//regular scene initiation

    ls.remove( animate );

}
```
With those 2 lines you get the following :

![Default loader](https://github.com/Astrak/LoadScreen.js/blob/master/default_loader.gif)

#Usage
##Full pattern
Methods are chainable, except `remove` and `setProgress`. Values are default :
```js
//First call : this appends an overlay on top of the canvas.
//complete style optional argument, values are default.
var style = {
    type: 'bar',//main look. Also 'circular'. 'custom' empties the info container and lets you fill it
    size: '150px',//width of the central info container, in px or in %
    background: '#333',
    progressBarContainer: '#444',
    progressBar: '#fb0',
    weight: '6px',//weight of the progress element, in px ('bar' type) or svg units ('circular')
    infoColor: '#666',
    sizeInfo: true,
    textInfo: [ 'Loading', 'Processing', 'Compiling' ]//Can also be set to a single string or to false
};
var ls = new LoadScreen( renderer, style );

//Resize is available. Can be a bit overkill on smartphones for loads < 5-6 seconds.
window.addEventListener( 'resize', function () { 
	renderer.setSize( width, height ); 
	ls.setSize( width, height ); 
});

//Options can be passed.
var options = {
    forcedStart: false,//start loading even if the canvas is out of sight (usually bad practice)
    verbose: false,//logs progress, process and compile duration + total load screen duration
    tweenDuration: .5//progress and remove tweens durations
};
ls.setOptions( options );

//Do things on progress events.
ls.onProgress( function ( progress ) { ... } );

//Define callbacks to fire after loading, processing and compiling.
ls.onComplete( init );

//Ready ! Usually needs a resources object (see next for formatting). Appends infos to overlay.
ls.start( resources );

//or if you want to handle the progress yourself, for any case not handled in the library
//(custom loader, display progress of a large script, or just for testing).
ls.start();
ls.setProgress( 0.5 );//etc.

//Remove the load screen ! Removal is tweened so we define a callback
ls.remove( animate );
```

##Format your resources
```js
//input
resources = {
    textures: {
        myTexture1: { 
            path: 'path/to/pic.jpg',
            fileSize: 2789,//in Ko
            //other threejs textures properties can be specified, like :
            minFilter: THREE.LinearFilter
        }
    },
    geometries: {
        myGeometry1: {
            path: 'path/to/geometry.json',
            fileSize: 9498,//in Ko
            //next four are optional
            computeNormals: false,//call geometry.computeVertexNormals()
            computeFlatNormals: false,//call geometry.computeFlatVertexNormals() (THREE.Geometry only)
            toBufferGeometry: false//force creation of a BufferGeometry
            copyUv1toUv2: false,//for AO and lightmap use (handled for THREE.BufferGeometry only)
        }
    },
    objects: {
        myObject1: {
            geometry: 'myGeometry1',
            material: new THREE.MeshStandardMaterial({//without maps
                color: 0xff8899, 
                side: THREE.DoubleSide 
            }),
            //next are optional
            type: 'mesh',//or 'points' or 'line', defaults to 'mesh'
            //specify any other threejs meshes or materials properties 
            aoMap: 'myTexture1',//assigned to material
            castShadow: false,//assigned to the mesh
            unknownOfThreejsParam : { 
                title: 'blabla', 
                content: 'blabla' 
            }//assigned to mesh.userData
        }
    }
};

//output
resources.textures.myTexture1;//THREE.Texture
resources.geometries.myGeometry1;//THREE.Geometry (some loaders can output a THREE.BufferGeometry)
resources.objects.myObject1;//THREE.Mesh
```

##Support
###Texture loaders
- [x] THREE.TextureLoader

###Material loaders
- [ ] THREE.MaterialLoader

###Geometry loaders
- [x] THREE.JSONLoader (threejs blender exporter)
- [x] THREE.PLYLoader
- [x] THREE.CTMLoader
- [x] THREE.VTKLoader
- [x] THREE.STLLoader
- [ ] THREE.BufferGeometryLoader

###Object loaders
- [ ] THREE.ThreeMFLoader
- [ ] THREE.AMFLoader
- [ ] THREE.AssimpLoader
- [ ] THREE.AssimpJSONLoader
- [ ] THREE.AWDLoader
- [ ] THREE.MMDLoader
- [ ] THREE.PlayCanvasLoader
- [ ] THREE.VRMLLoader
- [ ] THREE.UTF8Loader
- [ ] THREE.ObjectLoader

###Scene loaders
- [ ] THREE.OBJLoader
- [ ] THREE.FBXLoader
- [ ] THREE.ColladaLoader
- [ ] THREE.ColladaLoader (2)
- [ ] THREE.BabylonLoader

###Cubemap loaders
- [ ] THREE.CubeTextureLoader
- [ ] THREE.HDRCubeTextureLoader

###Animation loaders
- [ ] THREE.BVHLoader

#Roadmap
* Support more formats
* Support cubemaps and pmrem
* code the 'forcedStart' parameter
* check glTF resources organization for possible inspiration
* add fancy loader types
* handle custom message/warning/buttons before loading without setting style type to custom.. ?
* second progress bar at top of screen for assets loading after start
* add setStyle method for another style if further calls
* webgl loader instead of html ?
* extend to BabylonJS
* npm package
* react component

#License
MIT

#Dependencies : 
* Threejs
* Threejs loaders needed for your files
