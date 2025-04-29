<script setup>
import { ref, onMounted, reactive } from 'vue';
import * as THREE from 'three';
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader';

// 创建 Three.js 场景
const scene = new THREE.Scene();

// 设置相机
const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
camera.position.z = 5;
camera.position.y = 3;

camera.lookAt(new THREE.Vector3(0,0,0));

// 创建渲染器
const renderer = new THREE.WebGLRenderer();
renderer.setSize(window.innerWidth, window.innerHeight);
renderer.setClearColor(0xffc0cb); 

// 使用 ref 获取容器 DOM 元素
const threeContainer = ref(null);

// 创建光源
const ambientLight = new THREE.AmbientLight(0x404040, 60); // 强度增加
scene.add(ambientLight);
const pointLight = new THREE.PointLight(0xffffff, 60, 100); // 强度增加
const clock = new THREE.Clock();  
pointLight.position.set(5, 5, 5);
scene.add(pointLight);
// const axesHelper = new THREE.AxesHelper(5);

// // 将其添加到场景中
// scene.add(axesHelper);
// 加载 GLTF 模型
const loader = new GLTFLoader();
let cake = null;
let currentSection = reactive(0);
let mixer = null;
const bee = ref(null);
loader.load('src/1.gltf', function (gltf) {
  scene.add(gltf.scene);
  cake = gltf.scene;
});
loader.load('src/bee/model.gltf',function(gltf){
  bee.value = gltf.scene;
  bee.value.position.set(5, 2.5, 0);
  bee.value.rotation.set(0,90,0);
  if (gltf.animations && gltf.animations.length) {
    mixer = new THREE.AnimationMixer(gltf.scene); 
    gltf.animations.forEach((clip) => mixer.clipAction(clip).play());
  }
  bee.value = gltf.scene;
  scene.add(gltf.scene);
})

const scrollContainer = ref(null);
// 动画函数
const animate = () => {
  requestAnimationFrame(animate);
  if(cake){
    cake.rotation.y += 0.01;
  }
  // 更新动画
  if (mixer!=null) {
    if(clock){
      const delta = clock.getDelta();  // 获取每一帧的时间间隔
      mixer.update(delta);  // 更新动画
      if(bee.value!=null){
        bee.value.position.x-=delta;
      }
    }
  }
  renderer.render(scene, camera);
};

// 初始化 Three.js 渲染器并启动动画
onMounted(() => {
  threeContainer.value.appendChild(renderer.domElement);
  animate(); // 启动动画
});
// const onWheel = (event) => {
//   const delta = event.deltaY;
//   const container = scrollContainer.value;
//   const sectionHeight = container.clientHeight;
//   // if(currentSection!=0){
//   //   console.log("!=0");
//   //   if (delta > 0) {
//   //       // 向下滚动
//   //       // if (bee.value) {
//   //       //   bee.value.position.x -= 0.1; // bee 向右移动
//   //       // }
//   //       if (currentSection.value < sections.length - 1) {
//   //         currentSection.value++;
//   //       }
//   //     } else {
//   //       // 向上滚动
//   //       // if (bee.value) {
//   //       //   bee.value.position.x += 0.1; // bee 向左移动
//   //       // }
//   //       if (currentSection.value > 0) {
//   //         currentSection.value--;
//   //       }
//   //   }

//   //   // 自动滚动到当前部分
//   //   container.scrollTo({
//   //     top: currentSection.value * sectionHeight,
//   //     behavior: 'smooth',
//   //   });
//   // }
//   // else{
    
//   // }
//   console.log('delta:'+delta);
//     if (bee.value) {
//       bee.value.position.x += 0.1; // bee 向左移动
//     }
// };
const beeMove = (event)=>{
  //if(event.deltaY>0){
    //bee.value.position.x-=1;
  //}
  //else{
    //bee.value.position.x+=1;
  //}
}
</script>

<template>
  <main style="width: 100%;">
    <!-- 这是用于显示滚动内容的容器 -->
    <div :style="sectionStyle" @wheel="beeMove"></div>
    <div class="scroll-container" @wheel="onWheel" ref="scrollContainer" style="width: 100%;">
      <div class="section" v-for="(section, index) in sections" :key="index" :style="sectionStyle">
        <h2>{{ section.title }}</h2>
        <p>{{ section.content }}</p>
        <h2 class="section__title">Happy Birthday<br />
          <span>2025</span>
        </h2>
      </div>
    </div>
    <!-- Three.js 渲染的 canvas 作为背景 -->
    <div ref="threeContainer" class="three-container"></div>
  </main>
</template>

<script>
export default {
  data() {
    return {
      sections: [
        { title: "Section 1", content: "Content for section 1" },
        { title: "Section 2", content: "Content for section 2" },
        { title: "Section 3", content: "Content for section 3" },
        { title: "Section 4", content: "Content for section 4" },
      ],
      currentSection: 0, // 当前显示的部分
    };
  },
  computed: {
    sectionStyle() {
      return {
        height: '100vh',
        display: 'flex',
        // justifyContent: 'center',
        // alignItems: 'center',
        // backgroundColor: 'rgba(255, 255, 255, 0.7)',
        position: 'relative',
        zIndex: 1,
      };
    }
  },
  methods: {

  },
};
</script>

<style scoped>
/* Three.js 渲染的背景容器 */
.three-container {
  width: 100%;
  height: 100vh;
  position: fixed;  /* 使其固定在页面背景 */
  top: 0;
  left: 0;
  z-index: -1;  /* 保证 Three.js 背景在文字层下 */
}

/* 滚动容器 */
.scroll-container {
  height: 100vh;
  width: 100%;
  overflow-y: scroll;
  scroll-snap-type: y mandatory;
  -webkit-overflow-scrolling: touch;
  position: relative;
}

/* 滚动部分 */
.section {
  scroll-snap-align: start;
  padding: 20px;
  position: relative;
  z-index: 1;  /* 确保文字层位于 Three.js 背景上面 */
}

h2, p {
  z-index: 1;
}
</style>


<style scoped>
* {
  /* 采用怪异模式下的盒模型：元素的总高度和宽度包含内边距和边框(padding与border)  */
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  /* 没有滚动条 */
  overflow: hidden;
}

.section {
  display: flex;
  /* justify-content: center;
  align-items: center; */
  position: relative;
  min-height: 100vh;
  /* background: linear-gradient(135deg, #111, #222, #111); */
}
/* .section::before {
  content: "";
  position: absolute;
  width: 30vw;
  height: 30vw;
  border: 5vw solid #ff1062;
  border-radius: 50%;
  box-shadow: 0 0 0 1vw #222, 0 0 0 1.3vw #fff;
} */
.section .section__title {
  position: absolute;
  transform: skewY(-7deg);
  z-index: 10;
  color: #fff;
  /* text-align: center; */
  font-size: 5rem;
  line-height: 2em;
  text-shadow: 1px 1px 0 #ccc, 2px 2px 0 #ccc, 3px 3px 0 #ccc, 4px 4px 0 #ccc,
    5px 5px 0 #ccc;
    /* , 10px 10px 0 rgba(0, 0, 0, 0.2) */
  animation: floating 5s ease-in-out infinite;
}
/* .section .section__title span {
  text-shadow: 1px 1px 0 #ccc, 2px 2px 0 #ccc, 3px 3px 0 #ccc, 4px 4px 0 #ccc,
    5px 5px 0 #ccc, 6px 6px 0 #ccc, 7px 7px 0 #ccc, 8px 8px 0 #ccc,
    9px 9px 0 #ccc, 20px 20px 0 rgba(0, 0, 0, 0.2);
  font-weight: 700;
  font-size: 3em;
} */
/* .section i {
  position: absolute;
  background: #fff;
  border-radius: 50%;
  box-shadow: 0 0 10px #fff, 0 0 20px #fff, 0 0 40px #fff, 0 0 80px #fff;
  animation: animate linear infinite;
} */

@keyframes floating {
  0%,
  100% {
    transform: skewY(-7deg) translate(0, -20px);
  }
  50% {
    transform: skewY(-7deg) translate(0, 20px);
  }
}
/* 利用透明度设置星星明暗变化的动画效果 */
@keyframes animate {
  0% {
    opacity: 0;
  }
  10% {
    opacity: 1;
  }
  90% {
    opacity: 1;
  }
  100% {
    opacity: 0;
  }
}

</style>