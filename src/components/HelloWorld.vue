<script setup>
import TCanvas from './TCanvas.vue'
import TNode from './TNode.vue'
import * as THREE from 'three'
import { OrbitControls } from 'three/addons/controls/OrbitControls.js'
import { ref, onMounted } from 'vue'
import { ipcRenderer } from 'electron'

let cubesRef = ref([])
let canvasRef = ref()
let cubes = []
let time = 0.0
let renderer = undefined
let scene = undefined
let camera = undefined

onMounted(() => {
	console.log(window)
  init()
	  ipcRenderer.on('main-process-message', (_event, ...args) => {
	  	console.log(_event)
	  	console.log('[Receive Main-process message]:', ...args)
	})
});

function render() {
	time += 0.00001
	renderCubes(time)
	renderer.render(scene, camera)
	camera.updateProjectionMatrix();
	window.requestAnimationFrame(render)
}
function initCamera() {
	const fov = 75
	const aspect = 2
	const near = 0.001
	const far = 20

	camera = new THREE.PerspectiveCamera(fov, aspect, near, far);
	camera.position.set( 0, 0, -0.2);
}
function createLight() {
	const color = 0xFFFFFF
	const intensity = 1
	const light = new THREE.DirectionalLight(color, intensity)
	light.position.set(-1, 2, 4)
	scene.add(light)
}
function renderCubes(time) {
	cubes.forEach((cube, ndx) => {
			const speed = 1 + ndx * .1
			const rot = time * speed
			cube.rotation.x = rot
			cube.rotation.y = rot
	})
}
function init() {
	console.log(cubesRef.value)
	const cubesData = cubesRef.value.map(cube => {
		return {
			rotation: {
				x: 0,
				y: 0
			},
			position: {
				x: cube.x,
				y: cube.y,
				z: cube.z
			},
			create: cube.makeInstance
		}
	})
	
	const canvas = canvasRef.value
	renderer = new THREE.WebGLRenderer({ antialias: true, canvas })
	renderer.outputEncoding = THREE.sRGBEncoding;

	scene = new THREE.Scene()

	scene.background = new THREE.Color(0xe0e0e0)

	cubes = cubesData.map(cube => cube.create(scene, cube.position.x, cube.position.y, cube.position.z)
	, this)

	initCamera()
	const controls = new OrbitControls( camera, renderer.domElement );
	controls.addEventListener('change', render)
	controls.minDistance = 0.1
	controls.maxDistance = 0.1
	controls.target.set(0,0,-0.2)
	controls.update()


	createLight()


	window.requestAnimationFrame(render)
}

</script>

<template>
	<t-canvas>
		<template #canvas="canvasProps">
			<canvas :width="canvasProps.width" :height="canvasProps.height" ref="canvasRef"></canvas>
		</template>
		<template #objects="objects">
			<t-node v-for="cube in objects.cubes" :key="cube.id" :x="cube.x" :y="cube.y" :z="cube.z" :scene="scene" ref="cubesRef"></t-node>
		</template>
	</t-canvas>
</template>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
	margin: 40px 0 0;
}
ul {
	list-style-type: none;
	padding: 0;
}
li {
	display: inline-block;
	margin: 0 10px;
}
a {
	color: #42b983;
}
</style>
