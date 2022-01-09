<template>
  <!-- <DatGui>
    <DatFolder label="Red Light">
      <DatNumber
        v-model="gui.light1.position.x"
        :min="-10"
        :max="10"
        :step="0.01"
        label="X"
      />
      <DatNumber
        v-model="gui.light1.position.y"
        :min="-10"
        :max="10"
        :step="0.01"
        label="Y"
      />
      <DatNumber
        v-model="gui.light1.position.z"
        :min="-10"
        :max="10"
        :step="0.01"
        label="Z"
      />
      <DatNumber
        v-model="gui.light1.intensity"
        :min="0"
        :max="1000"
        :step="0.01"
        label="I"
      />
    </DatFolder>

    <DatFolder label="Green Light">
      <DatNumber
        v-model="gui.light2.position.x"
        :min="-10"
        :max="10"
        :step="0.01"
        label="X"
      />
      <DatNumber
        v-model="gui.light2.position.y"
        :min="-10"
        :max="10"
        :step="0.01"
        label="Y"
      />
      <DatNumber
        v-model="gui.light2.position.z"
        :min="-10"
        :max="10"
        :step="0.01"
        label="Z"
      />
      <DatNumber
        v-model="gui.light2.intensity"
        :min="0"
        :max="1000"
        :step="0.01"
        label="I"
      />
    </DatFolder>
  </DatGui> -->
  <Renderer
    alpha
    antialias
    ref="renderer"
    resize="window"
    :orbit-ctrl="{
      autoRotate: true,
      autoRotateSpeed: 4,
      maxDistance: 350,
      minDistance: 150,
      enablePan: false,
    }"
  >
    <Camera :position="{ z: 230 }" />
    <Scene background="#000000" ref="sceneRef">
      <DirectionalLight
        color="#fff"
        :intensity="3"
        :position="{ x: 0, y: 50, z: -20 }"
      />
      <AmbientLight
        color="#ffffff"
        :intensity="1"
        :position="{ x: 0, y: 20, z: 20 }"
      />
      <Icosahedron :radius="30" :detail="10">
        <PhongMaterial>
          <Texture src="assets/texture/star-nc8wkw.jpg" />
        </PhongMaterial>
      </Icosahedron>
      <Sphere :radius="150" :widthSegments="40" :heightSegments="40">
        <BasicMaterial>
          <Texture src="assets/texture/bg3-je3ddz.jpg" />
        </BasicMaterial>
      </Sphere>
    </Scene>
  </Renderer>
</template>

<script setup lang="ts">
// import { DatGui, DatNumber, DatFolder } from "dat-gui-vue";
import { ref, onMounted, reactive } from "vue";
import * as THREE from "three";
import {
  Scene,
  Torus,
  Icosahedron,
  EffectComposer,
  RenderPass,
  HalftonePass,
  UnrealBloomPass,
  Points,
  BasicMaterial,
  PhongMaterial,
  DirectionalLight,
  Sphere,
  Camera,
  Texture,
  Renderer,
  AmbientLight,
  PointLight,
  StandardMaterial,
  PointsMaterial,
  MeshPublicInterface,
  RendererPublicInterface,
} from "troisjs";
const loader = new THREE.TextureLoader();

const randomPointSphere = (radius: number) => {
  let theta = 2 * Math.PI * Math.random();
  let phi = Math.acos(2 * Math.random() - 1);
  let dx = 0 + radius * Math.sin(phi) * Math.cos(theta);
  let dy = 0 + radius * Math.sin(phi) * Math.sin(theta);
  let dz = 0 + radius * Math.cos(phi);
  return new THREE.Vector3(dx, dy, dz);
};

const rendererRef = ref();
const sceneRef = ref();

const starsGeometry = new THREE.BufferGeometry();
const particlesCount = 50;

const positionArray = new Float32Array(particlesCount * 3);
for (let i = 0; i < particlesCount * 3; i++) {
  positionArray[i] = (Math.random() - 0.5) * 5;
}
starsGeometry.setAttribute(
  "position",
  new THREE.BufferAttribute(positionArray, 3)
);

const textureStar = loader.load("assets/texture/p1-g3zb2a.png");
let starsMaterial = new THREE.PointsMaterial({
  size: 5,
  color: "#ffffff",
  transparent: true,
  opacity: 0.8,
  map: textureStar,
  blending: THREE.AdditiveBlending,
});
starsMaterial.depthWrite = false;
let stars = new THREE.Points(starsGeometry, starsMaterial);

onMounted(() => {
  sceneRef.value.scene.add(stars);

});

// sceneRef.value.add(stars);

// const texture1 = loader.load("assets/texture/p2-b3gnym.png");
// const texture2 = loader.load("assets/texture/p3-ttfn70.png");
// const texture4 = loader.load("assets/texture/p4-avirap.png");
// function createStars(texture: THREE.Texture, size: number, total: number) {
//   let pointGeometry = new THREE.BufferGeometry();
//   let pointMaterial = new THREE.PointsMaterial({
//     size: size,
//     map: texture,
//     blending: THREE.AdditiveBlending,
//   });

//   for (let i = 0; i < total; i++) {
//     let radius = THREE.MathUtils.randInt(149, 70);
//     let particles = randomPointSphere(radius);
//     pointGeometry.vertices.push(particles);
//   }
//   return new THREE.Points(pointGeometry, pointMaterial);
// }
// sceneRef.value.add(createStars(texture1, 15, 20));
// sceneRef.value.add(createStars(texture2, 5, 5));
// sceneRef.value.add(createStars(texture4, 7, 5));
// const torusRef = ref();
// const lightRef = ref();
// const pointer = {
//   mouse: {
//     x: 0,
//     y: 0,
//   },
//   target: {
//     x: 0,
//     y: 0,
//   },
//   window: {
//     x: 0,
//     y: 0,
//   },
// };
// const gui = reactive({
//   light1: {
//     position: {
//       x: -2.2,
//       y: -1.2,
//       z: 1.7,
//     },
//     intensity: 50,
//   },
//   light2: {
//     position: {
//       x: 7.4,
//       y: 6.6,
//       z: 1.7,
//     },
//     intensity: 250,
//   },
// });

// const onMouseMove = (event: any) => {
//   pointer.mouse.x = event.clientX - pointer.window.x;
//   pointer.mouse.y = event.clientY - pointer.window.y;
// };

// onMounted(() => {
//   // pointer.window.x = window.innerWidth / 2;
//   // pointer.window.y = window.innerHeight / 2;
//   // document.addEventListener("mousemove", onMouseMove);
//   // const renderer = rendererRef.value as RendererPublicInterface;
//   // const sphere = (sphereRef.value as MeshPublicInterface).mesh;
//   // const light = lightRef.value.light;
//   const scene = sceneRef.value.scene;
//   const geometry = new Three.TorusGeometry(0.7, 0.2, 16, 100);
//   const material = new Three.PointsMaterial({
//     size: 0.01,
//   });
//   const points = new Three.Points(geometry, material);

//   const particlesGeometry = new Three.BufferGeometry();
//   const particlesCount = 50000;
//   const positionArray = new Float32Array(particlesCount * 3);
//   for (let i = 0; i < particlesCount * 3; i++) {
//     positionArray[i] = (Math.random() - 0.5) * 5;
//   }
//   particlesGeometry.setAttribute(
//     "position",
//     new Three.BufferAttribute(positionArray, 3)
//   );
//   const particlesMaterial = new Three.PointsMaterial({
//     size: 0.005,
//     color: "blue",
//   });
//   const particlesMesh = new Three.Points(particlesGeometry, particlesMaterial);
//   scene.add(points, particlesMesh);
//   // const pointLightHelper = new Three.PointLightHelper(light, 1);
//   // scene.add(pointLightHelper);
//   // renderer.onBeforeRender(() => {
//   //   pointer.target.x = pointer.mouse.x * 0.001;
//   //   pointer.target.y = pointer.mouse.y * 0.001;
//   //   sphere!.rotation.x += 0.04;
//   //   sphere!.rotation.x += 0.5 * (pointer.target.x - sphere!.rotation.x);
//   //   sphere!.rotation.y += 0.5 * (pointer.target.y - sphere!.rotation.y);
//   //   sphere!.rotation.z += 0.5 * (pointer.target.y - sphere!.rotation.x);
//   // });
//   const renderer = rendererRef.value as RendererPublicInterface;
//   // const torus = (torusRef.value as MeshPublicInterface).mesh;
//   renderer.onBeforeRender(() => {
//     points!.rotation.y += 0.02;
//   });
// });
</script>

<style>
body {
  margin: 0;
  overflow: hidden;
  width: 100vw;
  height: 100vh;
  background-image: url("https://user-images.githubusercontent.com/26748614/96337246-f14d4580-1085-11eb-8793-a86d929e034d.jpg");
  background-size: cover;
  backdrop-filter: brightness(50%);
}

canvas {
  display: block;
}

#canvas_container {
  width: 100%;
  height: 100vh;
}

button {
  position: absolute;
  bottom: 5%;
  left: 50%;
  transform: translateX(-50%);
  border: 1px solid white;
  border-radius: 5px;
  font-size: 0.9rem;
  padding: 0.5rem 0.9em;
  background: #000000;
  color: white;
  -webkit-font-smoothing: antialiased;
  font-weight: bold;
  cursor: pointer;
  transition: all 0.3s;
}

button:hover {
  background: #ffffff;
  color: #000000;
}
</style>
