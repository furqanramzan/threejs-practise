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
  </DatGui>-->
  0.7, 0.2, 16, 100
  <Renderer antialias orbit-ctrl alpha ref="rendererRef" resize="window">
    <Camera :position="{ z: 3 }"></Camera>
    <Scene ref="sceneRef">
      <AmbientLight color="red"></AmbientLight>
      <!-- <Points ref="torusRef">
        <BoxGeometry :width="1" :height="1.5" :depth="1"></BoxGeometry>
        <PointsMaterial :props="{ size: 0.1 }"></PointsMaterial>
      </Points>-->
    </Scene>
    <EffectComposer>
      <RenderPass />
      <UnrealBloomPass :strength=".1" />
      <HalftonePass :radius=".1" :scatter="1" />
    </EffectComposer>
  </Renderer>
</template>

<script setup lang="ts">
import { DatGui, DatNumber, DatFolder } from "dat-gui-vue";
import { ref, onMounted, reactive } from "vue";
import * as THREE from "three";
import {
  Scene,
  Torus,
  EffectComposer,
  RenderPass,
  HalftonePass,
  UnrealBloomPass,
  Points,
  Camera,
  Texture,
  Renderer,
  AmbientLight,
  PointLight,
  StandardMaterial,
  PointsMaterial,
  BoxGeometry,
  MeshPublicInterface,
  RendererPublicInterface,
} from "troisjs";
import { MeshSurfaceSampler } from 'three/examples/jsm/math/MeshSurfaceSampler.js';

const rendererRef = ref();
const sceneRef = ref();
const torusRef = ref();
const lightRef = ref();
const pointer = {
  mouse: {
    x: 0,
    y: 0,
  },
  target: {
    x: 0,
    y: 0,
  },
  window: {
    x: 0,
    y: 0,
  },
};
const gui = reactive({
  light1: {
    position: {
      x: -2.2,
      y: -1.2,
      z: 1.7,
    },
    intensity: 50,
  },
  light2: {
    position: {
      x: 7.4,
      y: 6.6,
      z: 1.7,
    },
    intensity: 250,
  },
});

const onMouseMove = (event: any) => {
  pointer.mouse.x = event.clientX - pointer.window.x;
  pointer.mouse.y = event.clientY - pointer.window.y;
};

onMounted(() => {
  // pointer.window.x = window.innerWidth / 2;
  // pointer.window.y = window.innerHeight / 2;
  // document.addEventListener("mousemove", onMouseMove);
  // const renderer = rendererRef.value as RendererPublicInterface;
  // const sphere = (sphereRef.value as MeshPublicInterface).mesh;
  // const light = lightRef.value.light;
  const scene = sceneRef.value.scene;
  const group = new THREE.Group();
  scene.add(group);

  // Create a torus know with basic geometry & material
  const geometry = new THREE.BoxGeometry(1, 1.5, 1);
  const torusKnot = new THREE.Mesh(geometry);

  // Instantiate a sampler so we can use it later
  const sampler = new MeshSurfaceSampler(torusKnot).build();

  // Array used to store all points coordinates
  const vertices = [];
  // Create a dummy Vector to store the sampled coordinates
  const tempPosition = new THREE.Vector3();
  // Loop to sample a coordinate for each points
  for (let i = 0; i < 10000; i++) {
    // Sample a random position in the torus
    sampler.sample(tempPosition);
    // Push the coordinates of the sampled coordinates into the array
    vertices.push(tempPosition.x, tempPosition.y, tempPosition.z);
  }

  // Create a geometry for the points
  const pointsGeometry = new THREE.BufferGeometry();
  // Define all points positions from the previously created array
  pointsGeometry.setAttribute('position', new THREE.Float32BufferAttribute(vertices, 3));
  // Define the matrial of the points
  const pointsMaterial = new THREE.PointsMaterial({
    color: 0xff61d5,
    size: 0.03
  });
  // Create an instance of points based on the geometry & material
  const points = new THREE.Points(pointsGeometry, pointsMaterial);
  // Add them into the main group
  group.add(points);
  // const geometry = new Three.TorusGeometry(0.7, 0.2, 16, 100);
  // const material = new Three.PointsMaterial({
  //   size: 0.01,
  // });
  // const points = new Three.Points(geometry, material);

  // const particlesGeometry = new Three.BufferGeometry();
  // const particlesCount = 50000;
  // const positionArray = new Float32Array(particlesCount * 3);
  // for (let i = 0; i < particlesCount * 3; i++) {
  //   positionArray[i] = (Math.random() - 0.5) * 5;
  // }
  // particlesGeometry.setAttribute(
  //   "position",
  //   new Three.BufferAttribute(positionArray, 3)
  // );
  // const particlesMaterial = new Three.PointsMaterial({
  //   size: 0.005,
  //   color: "blue"
  // });
  // const particlesMesh = new Three.Points(particlesGeometry, particlesMaterial);
  // scene.add(particlesMesh);
  // scene.add(points);
  // const pointLightHelper = new Three.PointLightHelper(light, 1);
  // scene.add(pointLightHelper);
  // renderer.onBeforeRender(() => {
  //   pointer.target.x = pointer.mouse.x * 0.001;
  //   pointer.target.y = pointer.mouse.y * 0.001;
  //   sphere!.rotation.x += 0.04;
  //   sphere!.rotation.x += 0.5 * (pointer.target.x - sphere!.rotation.x);
  //   sphere!.rotation.y += 0.5 * (pointer.target.y - sphere!.rotation.y);
  //   sphere!.rotation.z += 0.5 * (pointer.target.y - sphere!.rotation.x);
  // });
  const renderer = rendererRef.value as RendererPublicInterface;
  // const torus = (torusRef.value as MeshPublicInterface).mesh;
  // renderer.onBeforeRender(() => {
  //   torus!.rotation.y += 0.02;
  // });
});
</script>

<style>
body,
html {
  margin: 0;
  background: rgb(24, 24, 24);
}
canvas {
  display: block;
}
</style>
