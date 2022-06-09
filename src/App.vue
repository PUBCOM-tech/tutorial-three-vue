<script>
import * as THREE from 'three'

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

    /**
     * reactiveなデータ
     * * ここでThree.jsのデータを定義すると、重くなるか動かなくなるので注意
     *   * https://stackoverflow.com/a/65732553
     */
    return {}
  },

  mounted() {
    this.init()
    this.addBox()
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

      this.camera.position.z = 5
    },

    addBox() {
      const geometry = new THREE.BoxGeometry()
      const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 })
      this.cube = new THREE.Mesh(geometry, material)
      this.scene.add(this.cube)
    },

    animate() {
      requestAnimationFrame(this.animate)

      this.cube.rotation.x += 0.01
      this.cube.rotation.y += 0.01

      this.renderer.render(this.scene, this.camera)
    }
  }
}
</script>

<template>
  <canvas ref="canvasRef" class="fullscreen"></canvas>
</template>

<style scoped>
.fullscreen {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}
</style>
