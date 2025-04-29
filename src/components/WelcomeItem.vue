<template>
  <div ref="threeContainer" class="three-container"></div>
</template>

<script>
import * as THREE from 'three';
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader';


export default {
  name: 'ThreeScene',
  mounted() {
    // 创建场景
    const scene = new THREE.Scene();

    // 设置相机
    const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
    camera.position.z = 5;

    // 创建渲染器
    const renderer = new THREE.WebGLRenderer();
    renderer.setSize(window.innerWidth, window.innerHeight);
    this.$refs.threeContainer.appendChild(renderer.domElement);

    // 创建一个立方体
    const geometry = new THREE.BoxGeometry();
    const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
    const cube = new THREE.Mesh(geometry, material);
    scene.add(cube);
    const loader = new GLTFLoader();

    loader.load('../assets/1.gltf' , function ( gltf ) {

      scene.add( gltf.scene );

    });

    // 动画函数
    const animate = () => {
      requestAnimationFrame(animate);

      // 让立方体旋转
      cube.rotation.x += 0.01;
      cube.rotation.y += 0.01;

      // 渲染场景
      renderer.render(scene, camera);
    };

    animate();
  },
  beforeDestroy() {
    // 清理 Three.js 渲染器
    this.$refs.threeContainer.removeChild(this.$refs.threeContainer.firstChild);
  }
};
</script>

<style scoped>
.three-container {
  width: 100%;
  height: 100vh;
}
</style>
