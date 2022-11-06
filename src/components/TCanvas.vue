<template>
	<slot name="canvas" width="800" height="600" ref="c" ></slot>
	<slot name="objects" :cubes="cubes"></slot>
</template>

<script>

export default {
	name: 'TCanvas',
	data() {
		return {
			cubes: [
			{
				id: 1,
				x: -3,
				y: -5,
				z: 0
			},
			{
				id: 2,
				x: 1,
				y: 0,
				z: 1
			}]
		}
	}
}
</script>