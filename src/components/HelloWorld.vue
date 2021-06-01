<template>
  <div>
    <div id="container"></div>
  </div>
</template>

<script>
import * as THREE from "three"
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js"
import { ColladaLoader } from "three/examples/jsm/loaders/ColladaLoader.js"
let container, clock
let camera, scene, renderer, elf
export default {
  data() {
    return {
      publicPath: process.env.BASE_URL
    }
  },
  mounted() {
    this.init()
    this.animate()
  },
  methods: {
    init() {
      console.log(1111)
      container = document.getElementById("container")

      camera = new THREE.PerspectiveCamera(
        45,
        window.innerWidth / window.innerHeight,
        0.1,
        2000
      )
      camera.position.set(8, 10, 6)
      camera.lookAt(0, 3, 0)

      scene = new THREE.Scene() // 创建场景
      clock = new THREE.Clock() // 创建时间
      const loadingManager = new THREE.LoadingManager(function() {
        scene.add(elf)
      })
      // 加载collad文件
      const loader = new ColladaLoader(loadingManager)
      console.log(333)
      loader.load(`${this.publicPath}static/models/collada/house02.dae`, function(collada) {
        elf = collada.scene
      })
      //
      const ambientLight = new THREE.AmbientLight(0xcccccc, 0.4)
      scene.add(ambientLight)

      const directionalLight = new THREE.DirectionalLight(0xffffff, 0.8)
      directionalLight.position.set(1, 1, 0).normalize()
      scene.add(directionalLight)

      //

      renderer = new THREE.WebGLRenderer({ antialias: true })
      renderer.setSize(window.innerWidth, window.innerHeight)
      container.appendChild(renderer.domElement) // 插入canves对象
      // renderer.render(scene, camera)

      const controls = new OrbitControls(camera, renderer.domElement)
      controls.addEventListener("change", () => {
        console.log(222)
        renderer.render(scene, camera)
      })
      window.addEventListener("resize", this.onWindowResize)
    },
    onWindowResize() {
      camera.aspect = window.innerWidth / window.innerHeight
      camera.updateProjectionMatrix()

      renderer.setSize(window.innerWidth, window.innerHeight)
    },
    animate() {
      requestAnimationFrame(this.animate)
      // renderer.render(scene, camera)
      this.render() // 实现动画
    },
    render() {
      // 获取动画
      const delta = clock.getDelta()
      if (elf !== undefined) {
        elf.rotation.z += delta * 0.5
      }
      renderer.render(scene, camera)
    }
  }
}
</script>
<style>
#container {
  position: absolute;
  width: 600px;
  height: 600px;
}
</style>
