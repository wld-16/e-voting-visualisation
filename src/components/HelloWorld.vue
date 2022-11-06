<template>
	<t-canvas>
		<template #canvas="canvasProps">
			<canvas :width="canvasProps.width" :height="canvasProps.height" ref="c"></canvas>
		</template>
		<template #objects="objects">
			<t-node v-for="cube in objects.cubes" :key="cube.id" :x="cube.x" :y="cube.y" :z="cube.z" :scene="scene" ref="cubes"></t-node>
		</template>
	</t-canvas>
</template>

<script>
import TCanvas from './TCanvas'
import TNode from './TNode'
import * as THREE from 'three'
import { OrbitControls } from 'three/addons/controls/OrbitControls.js'
import { useIpcRendererOn } from '@vueuse/electron'


export default {
	name: 'HelloWorld',
	components: {
		TCanvas,
		TNode
	},
	data() {
		return {
			cubes: [],
			time: 0.0
		}
	},
	methods: {
		render() {
				this.time += 0.00001
				this.renderCubes(this.time)
				this.renderer.render(this.scene, this.camera)
				this.camera.updateProjectionMatrix();
				window.requestAnimationFrame(this.render)
		},
		initCamera() {
				const fov = 75
				const aspect = 2
				const near = 0.001
				const far = 20

				this.camera = new THREE.PerspectiveCamera(fov, aspect, near, far);
				this.camera.position.set( 0, 0, -0.2);
		},
		createLight() {
				const color = 0xFFFFFF
				const intensity = 1
				const light = new THREE.DirectionalLight(color, intensity)
				light.position.set(-1, 2, 4)
				this.scene.add(light)
		},
		renderCubes(time) {
				this.cubes.forEach((cube, ndx) => {
						const speed = 1 + ndx * .1
						const rot = time * speed
						cube.rotation.x = rot
						cube.rotation.y = rot
				})
		},
		init() {
			const cubes = this.$refs.cubes.map(cube => {
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
			
			const canvas = this.$refs.c
			this.renderer = new THREE.WebGLRenderer({ antialias: true, canvas })
			this.renderer.outputEncoding = THREE.sRGBEncoding;

			this.scene = new THREE.Scene()

			this.scene.background = new THREE.Color(0xe0e0e0)

			this.cubes = cubes.map(cube => cube.create(this.scene, cube.position.x, cube.position.y, cube.position.z)
			, this)

			this.initCamera()
			const controls = new OrbitControls( this.camera, this.renderer.domElement );
			controls.addEventListener('change', this.render)
			controls.minDistance = 0.1
			controls.maxDistance = 0.1
			controls.target.set(0,0,-0.2)
			controls.update()


			this.createLight()


			window.requestAnimationFrame(this.render)
		}
	},
	mounted() {
		this.init()
			useIpcRendererOn('custom-event', (event, ...args) => {
			console.log(args)
		})
	},
	props: {
		msg: String
	}
}
</script>

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
