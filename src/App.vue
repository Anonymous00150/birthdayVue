<script setup>
import { ref, onMounted } from 'vue';
import * as THREE from 'three';
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader';

// 创建场景
const scene = new THREE.Scene();

// 设置相机
const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
camera.position.z = 5;

// 创建渲染器
const renderer = new THREE.WebGLRenderer();
renderer.setSize(window.innerWidth, window.innerHeight);
renderer.setClearColor(0xffc0cb); 

// 使用 ref 获取容器 DOM 元素
const threeContainer = ref(null);

// 创建立方体
const geometry = new THREE.BoxGeometry();
const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
// const cube = new THREE.Mesh(geometry, material);
// scene.add(cube);
// 环境光：可以增加环境光的强度，让模型亮一些
const ambientLight = new THREE.AmbientLight(0x404040, 30);  // 强度增加到2
scene.add(ambientLight);

// 点光源：可以增加点光源的强度
const pointLight = new THREE.PointLight(0xffffff, 30, 100); // 强度增加到2
pointLight.position.set(5, 5, 5);
scene.add(pointLight);

// 加载 GLTF 模型
const loader = new GLTFLoader();
let cake = null;
loader.load('src/1.gltf', function (gltf) {
  scene.add(gltf.scene);
  cake = gltf.scene;
});

// 动画函数
const animate = () => {
  requestAnimationFrame(animate);

  // // 让立方体旋转
  // cube.rotation.x += 0.01;
  // cube.rotation.y += 0.01;

  cake.rotation.y += 0.01;

  // 渲染场景
  renderer.render(scene, camera);
};

onMounted(() => {
  // 在组件挂载后，将渲染器的 canvas 添加到 DOM
  threeContainer.value.appendChild(renderer.domElement);
  animate(); // 启动动画
});
</script>

<template>
  <!-- <header>

  </header> -->

  <main>
    <!-- 这是用于显示 Three.js 渲染的容器 -->
    <div ref="threeContainer" class="three-container"></div>
  </main>
</template>

<style scoped>
.three-container {
  width: 100%;
  height: 100vh;
}

header {
  line-height: 1.5;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
