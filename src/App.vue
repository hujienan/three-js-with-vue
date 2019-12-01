<template>
  <div id="app">
  </div>
</template>

<script>
import * as THREE from './three/three.js';
const OrbitControls = require('three-orbit-controls')(THREE)
let container
let camera;
let renderer;
let scene;
let cube;

// controls.enableDamping = true; // an animation loop is required when either damping or auto-rotation are enabled
// controls.dampingFactor = 0.05;
// controls.screenSpacePanning = false;
export default {
  methods: {
    init: function () {
      container = document.querySelector( '#app' );
      scene = new THREE.Scene();
      //Does not work by setting background for scene
			scene.background = new THREE.Color( 'skyblue' );

      camera = new THREE.PerspectiveCamera( 35, container.clientWidth / container.clientWidth, 0.1, 100 );
      camera.position.set( -4, 4, 10 );
      const geometry = new THREE.BoxBufferGeometry( 2, 2, 2 );
      const textureLoader = new THREE.TextureLoader();
      const texture = textureLoader.load( 'https://images.unsplash.com/photo-1515387784663-e2e29a23f69e?ixlib=rb-1.2.1&w=1000&q=80');
      texture.encoding = THREE.sRGBEncoding;
      texture.anisotropy = 16;
      const material = new THREE.MeshStandardMaterial( {
        map: texture,
      } );
      cube = new THREE.Mesh( geometry, material );
      scene.add( cube );
      this.createLights();
      renderer = new THREE.WebGLRenderer({antialias: true});
      renderer.setSize( container.clientWidth, container.clientHeight );
      renderer.setPixelRatio( window.devicePixelRatio );
      renderer.gammaFactor = 2.2;
      renderer.gammaOutput = true;
      renderer.physicallyCorrectLights = true;
      container.appendChild( renderer.domElement );
      renderer.setAnimationLoop(() => {
        this.update();
        this.render();
      });
      // this.animate()
    },
    createLights: function () {
      const ambientLight = new THREE.HemisphereLight(
        0xddeeff, // sky color
        0x202020, // ground color
        5, // intensity
      );

      const mainLight = new THREE.DirectionalLight( 0xffffff, 5 );
      mainLight.position.set( 10, 10, 10 );

      scene.add( ambientLight, mainLight );
    },
    update: function () {
      cube.rotation.x += 0.01;
      cube.rotation.y += 0.01;
      // cube.rotation.z += 0.01;
    },
    render: function () {
        renderer.render( scene, camera );
    },
    onWindowResize: function () {
        camera.aspect = container.clientWidth / container.clientHeight;
        camera.updateProjectionMatrix();
        renderer.setSize( container.clientWidth, container.clientHeight );
    }

  },
  mounted: function () {
      this.init();
      window.addEventListener( 'resize', this.onWindowResize );
      new OrbitControls(camera, renderer.domElement);
  }
}
</script>

<style>
  #app {
    position: absolute;
    width: 100%;
    height: 100%;
  }
</style>
