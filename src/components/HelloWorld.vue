<template>
  <div id="scene-container" ref="sceneContainer"></div>
</template>

<script>
import * as THREE from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader.js";
import Stats from "stats.js";

export default {
  name: "HelloWorld",
  methods: {
    init() {
      // set container

      this.container = this.$refs.sceneContainer;
      console.log(this.container.clientWidth);

      // add stats
      this.stats = new Stats();
      this.container.appendChild(this.stats.dom);

      // add camera
      const fov = 10; // Field of view
      const aspect = this.container.clientWidth / this.container.clientHeight;
      const near = 0.1; // the near clipping plane
      const far = 30; // the far clipping plane
      const camera = new THREE.PerspectiveCamera(fov, aspect, near, far);
      camera.position.set(0, 5, 10);
      this.camera = camera;

      // create scene
      this.scene = new THREE.Scene();
      this.scene.background = new THREE.Color("black");

      // add lights
      const ambientLight = new THREE.HemisphereLight(
        "0xffffff", // bright sky color
        0x222222, // dim ground color
        1 // intensity
      );
      const mainLight = new THREE.DirectionalLight(0xffffff, 4.0);
      mainLight.position.set(10, 10, 10);
      this.scene.add(ambientLight, mainLight);

      // add controls
      this.controls = new OrbitControls(this.camera, this.container);

      // create renderer
      this.renderer = new THREE.WebGLRenderer({ antialias: true });
      this.renderer.setSize(
        this.container.clientWidth,
        this.container.clientHeight
      );
      this.renderer.setPixelRatio(window.devicePixelRatio);
      this.renderer.gammaFactor = 2.2;
      this.renderer.outputEncoding = THREE.sRGBEncoding;
      this.renderer.physicallyCorrectLights = true;
      this.container.appendChild(this.renderer.domElement);

      // set aspect ratio to match the new browser window aspect ratio
      this.camera.aspect =
        this.container.clientWidth / this.container.clientHeight;
      this.camera.updateProjectionMatrix();
      this.renderer.setSize(
        this.container.clientWidth,
        this.container.clientHeight
      );

      // create animation moving around

      const loader = new GLTFLoader();

      loader.load(
        "/three-assets/ghost_in_the_shell_geisha_mask/scene.gltf",
        (gltf) => {
          this.scene.add(gltf.scene);
        },
        undefined,
        undefined
      );

      this.renderer.setAnimationLoop(() => {
        this.render();
      });
    },
    render() {
      this.renderer.render(this.scene, this.camera);
      this.stats.update();
    },
  },
  mounted() {
    this.init();
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#scene-container {
  height: 100%;
}
</style>
