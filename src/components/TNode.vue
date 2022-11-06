<script>
import * as THREE from 'three'

export default {
  name: 'TNode',
  props: {
    x: Number,
    y: Number,
    z: Number
  },
  methods: {
    makeInstance(scene, x, y, z) {
      const geometry = new THREE.BoxGeometry( 1, 1, 1 );
      const material = new THREE.MeshBasicMaterial( {color: 0x00ff00} );
      const cube = new THREE.Mesh( geometry, material );
      scene.add(cube)
      cube.position.x = x
      cube.position.y = y
      cube.position.z = z
      return cube
    },
  }
}
</script>