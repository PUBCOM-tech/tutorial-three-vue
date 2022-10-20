<script>
import * as THREE from 'three'
import { HTMLMesh } from 'three/examples/jsm/interactive/HTMLMesh.js'
import { InteractiveGroup } from 'three/examples/jsm/interactive/InteractiveGroup.js'
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js'
import { CSS3DRenderer, CSS3DObject } from 'three/examples/jsm/renderers/CSS3DRenderer.js'

export default {
  data() {
    /**
     * non-reactiveなデータ
     * * https://stackoverflow.com/a/54907413
     */
    this.scene = null
    this.camera = null
    this.renderer = null

    this.sceneCss = null
    this.rendererCss = null

    this.cube = null
    this.shop = null

    this.htmlMesh = null
    this.css3dObject = null

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
  this.addCss3dObject()
  this.animate()
},

  methods: {
    init() {
      /**
       * WebGL
       */
      this.scene = new THREE.Scene()
      this.camera = new THREE.PerspectiveCamera(
        75, // FOV
        window.innerWidth / window.innerHeight, // aspect ratio
        0.1, // near
        1000 // far
      )
      this.renderer = new THREE.WebGLRenderer({ canvas: this.$refs.canvasRef })

      /**
       * CSS3D
       */
      this.sceneCss = new THREE.Scene()
      this.rendererCss = new CSS3DRenderer()
      this.$refs.css3dRef.appendChild(this.rendererCss.domElement)

      // レンダリング解像度
      this.renderer.setSize(
        window.innerWidth,
        window.innerHeight,
        false
      )
      this.rendererCss.setSize(
        window.innerWidth,
        window.innerHeight
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
      this.htmlMesh.position.set(-4, 5, 5)
      this.htmlMesh.scale.setScalar(10)

      // HTMLMeshはInteractiveGroupを経由しないとボタンも押せない
      const group = new InteractiveGroup(this.renderer, this.camera)
      group.add(this.htmlMesh)

      this.scene.add(group)
    },

    addCss3dObject() {
      this.css3dObject = new CSS3DObject(this.$refs.modalCss3dRef)
      this.css3dObject.position.set(4, 5, 5)
      this.css3dObject.scale.setScalar(0.01)

      this.sceneCss.add(this.css3dObject)
    },

    onClick() {
      alert('hello')
    },

    animate() {
      requestAnimationFrame(this.animate)

      this.cube.rotation.x += 0.01
      this.cube.rotation.y += 0.01

      this.htmlMesh.rotation.x += 0.01
      this.css3dObject.rotation.x += 0.01

      this.renderer.render(this.scene, this.camera)
      this.rendererCss.render(this.sceneCss, this.camera)
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
  <!-- idを追加 -->
  <div id="container3d" class="fullscreen">
    <canvas ref="canvasRef" class="fullscreen"></canvas>
    <div ref="css3dRef"></div>
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

    <!-- ↓これを追加 -->
    <div ref="modalCss3dRef" class="modal3d">
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
#container3d {
  overflow: hidden;
}
.fullscreen {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}
.modal3d {
  width: 400px;
  height: 300px;
  padding: 20px;
  border: dotted 10px green;
  background-color: black;
  color: #f0f0f0;
  padding: 5px 15px;
  border-radius: 20px;
  letter-spacing: 1px;
}
</style>
