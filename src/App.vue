<script>
import * as THREE from 'three'
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js'
import { HTMLMesh } from 'three/examples/jsm/interactive/HTMLMesh.js'
import { InteractiveGroup } from 'three/examples/jsm/interactive/InteractiveGroup.js'

export default {
  data() {
    /**
     * non-reactiveなデータ
     * * https://stackoverflow.com/a/54907413
     */
    this.scene = null
    this.camera = null
    this.renderer = null

    this.cube = null
    this.shop = null

    this.htmlMesh = null

    /**
     * reactiveなデータ
     * * ここでThree.jsのデータを定義すると、重くなるか動かなくなるので注意
     *   * https://stackoverflow.com/a/65732553
     */
    return {}
  },

  mounted() {
    this.init()
    this.addModels()
    this.addHtmlMesh()
    this.animate()
  },

  methods: {
    init() {
      this.scene = new THREE.Scene()
      this.camera = new THREE.PerspectiveCamera(
        75, // FOV
        window.innerWidth / window.innerHeight, // aspect ratio
        0.1, // near
        1000 // far
      )
      this.renderer = new THREE.WebGLRenderer({ canvas: this.$refs.canvasRef })

      // レンダリング解像度
      this.renderer.setSize(
        window.innerWidth,
        window.innerHeight,
        false
      )

      // 光源
      const light = new THREE.AmbientLight(0xe0e0e0)
      this.scene.add(light)

      // カメラ位置
      this.camera.position.z = 10
      this.camera.position.y = 3
    },

    addModels() {
      // 箱
      const geometry = new THREE.BoxGeometry()
      const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 })
      this.cube = new THREE.Mesh(geometry, material)
      this.scene.add(this.cube)

      // お店
      this.shop = new Shop(this.scene)
    },

    addHtmlMesh() {
      this.htmlMesh = new HTMLMesh(this.$refs.modalRef)
      this.htmlMesh.position.set(0, 5, 5)
      this.htmlMesh.scale.setScalar(10)

      // HTMLMeshはInteractiveGroupを経由しないとボタンも押せない
      const group = new InteractiveGroup(this.renderer, this.camera)
      group.add(this.htmlMesh)

      this.scene.add(group)
    },

    onClick() {
      alert('hello')
    },

    animate() {
      requestAnimationFrame(this.animate)

      this.cube.rotation.x += 0.01
      this.cube.rotation.y += 0.01

      this.htmlMesh.rotation.x += 0.01

      this.renderer.render(this.scene, this.camera)
    }
  }
}

class Shop {
  constructor(scene) {
    const loader = new GLTFLoader()

    loader.load(
      '/3d/copain.glb',

      (gltf) => {
        scene.add(gltf.scene)
      },

      undefined,

      (error) => {
        console.error(error)
      }
    )
  }
}
</script>

<template>
  <div>
    <canvas ref="canvasRef" class="fullscreen"></canvas>
    <div ref="modalRef" class="modal3d">
      <p>
        Lorem ipsum dolor sit amet consectetur adipisicing<br />
        elit. Possimus expedita necessitatibus, at magni<br />
        optio sequi cumque repellat fugit rem ratione <br />
        laudantium perferendis officiis eveniet alias atque<br />
        debitis, itaque qui. Repellendus!
      </p>
      <button @click="onClick">Hello</button>
    </div>
  </div>
</template>

<style scoped>
.fullscreen {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}
.modal3d {
  width: 400px;
  height: 200px;
  padding: 20px;
  border-width: 10px;
  border-color: white;
  border-style: solid;
  background-color: bisque;
}
</style>
