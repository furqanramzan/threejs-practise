<template>
  <DatGui>
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
  </DatGui>
  <Renderer antialias orbit-ctrl alpha ref="rendererRef" resize="window">
    <Camera :position="{ z: 10 }"> </Camera>
    <Scene ref="sceneRef">
      <PointLight
        :intensity="1"
        color="#ffffff"
        :position="{ x: 200, y: 200, z: 200 }"
      ></PointLight>
      <Sphere :radius="100" :width-segments="64" :height-segments="64">
        <PhysicalMaterial></PhysicalMaterial>
      </Sphere>
      <PointLight :intensity="50" :position="{ x: 2, y: 3, z: 4 }"></PointLight>
      <PointLight
        :intensity="gui.light1.intensity"
        :position="{
          x: gui.light1.position.x,
          y: gui.light1.position.y,
          z: gui.light1.position.z,
        }"
        color="red"
      ></PointLight>
      <PointLight
        :intensity="gui.light2.intensity"
        :position="{
          x: gui.light2.position.x,
          y: gui.light2.position.y,
          z: gui.light2.position.z,
        }"
        color="green"
        ref="lightRef"
      ></PointLight>
      <Sphere ref="sphereRef">
        <StandardMaterial
          color="#292929"
          :props="{ roughness: 0.2, metalness: 0.7 }"
        >
          <Texture src="/assets/textures/normalMap.jpeg" />
        </StandardMaterial>
      </Sphere>
    </Scene>
  </Renderer>
</template>

<script setup lang="ts">
import { DatGui, DatNumber, DatFolder } from "dat-gui-vue";
import { ref, onMounted, reactive } from "vue";
import * as three from "three";
import {
  Scene,
  Sphere,
  Camera,
  Texture,
  Renderer,
  PointLight,
  StandardMaterial,
  PhysicalMaterial,
  MeshPublicInterface,
  RendererPublicInterface,
} from "troisjs";

const rendererRef = ref();
const sceneRef = ref();
const sphereRef = ref();
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
  pointer.window.x = window.innerWidth / 2;
  pointer.window.y = window.innerHeight / 2;

  document.addEventListener("mousemove", onMouseMove);

  const renderer = rendererRef.value as RendererPublicInterface;
  const sphere = (sphereRef.value as MeshPublicInterface).mesh;
  const light = lightRef.value.light;
  const scene = sceneRef.value.scene;

  const pointLightHelper = new three.PointLightHelper(light, 1);
  scene.add(pointLightHelper);

  renderer.onBeforeRender(() => {
    pointer.target.x = pointer.mouse.x * 0.001;
    pointer.target.y = pointer.mouse.y * 0.001;

    sphere!.rotation.x += 0.04;
    sphere!.rotation.x += 0.5 * (pointer.target.x - sphere!.rotation.x);
    sphere!.rotation.y += 0.5 * (pointer.target.y - sphere!.rotation.y);
    sphere!.rotation.z += 0.5 * (pointer.target.y - sphere!.rotation.x);
  });
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
