<script setup>
import { onMounted } from 'vue'
import * as THREE from 'three'
import * as PANOLENS from 'panolens'

const panorama = new PANOLENS.ImagePanorama('https://pchen66.github.io/Panolens/examples/asset/textures/equirectangular/tunnel.jpg');

function tapPos(e) {
	var intersects = e.raycaster.intersectObject(e.panorama, true);
	if (intersects.length > 0) {
		var point = intersects[0].point;
		var panoramaWorldPosition = e.panorama.getWorldPosition(new THREE.Vector3);

		var outputPosition = new THREE.Vector3(
			-(point.x - panoramaWorldPosition.x).toFixed(2),
			(point.y - panoramaWorldPosition.y).toFixed(2),
			(point.z - panoramaWorldPosition.z).toFixed(2)
		);

		return outputPosition;
	} else {
		return false;
	}
}

onMounted(() => {
	const viewer = new PANOLENS.Viewer({ container: document.querySelector('#container') });
	viewer.add(panorama);

	panorama.addEventListener('click', () => {
		let pos = tapPos(viewer);
		if (pos) {
			const infospot = new PANOLENS.Infospot(300);
			const spotPos = infospot.position;

			spotPos.set(parseInt(pos.x), parseInt(pos.y), parseInt(pos.z));
			panorama.add(infospot);
			infospot.show();
			infospot.focus();

		}
		const x = document.querySelector('.p_x');
		const y = document.querySelector('.p_y');
		const z = document.querySelector('.p_z');

		x.value = parseInt(pos.x)
		y.value = parseInt(pos.y)
		z.value = parseInt(pos.z)
	})

})


</script>
	  
<template>
	<h1 class="ml-8 text-lg font-bold">Panorama Image MousePos Get</h1>
	<br>
	<div class="posLabel w-[1200px] flex justify-center items-center m-5 rounded-lg bg-transparent bg-slate-800 pl-5">
		<div class="w-[320px] flex flex-col gap-4 mr-5">
			<label class="text-slate-400 text-lg font-bold">x-pos:</label>
			<input type="text" name="p_x" class="p_x" disabled />
			<label class="text-slate-400 text-lg font-bold">y-pos:</label>
			<input type="text" name="p_y" class="p_y" disabled />
			<label class="text-slate-400 text-lg font-bold">z-pos:</label>
			<input type="text" name="p_z" class="p_z" disabled />
		</div>
		<div id="container" class="mx-auto"></div>
	</div>
</template>
	  
<style>
html,
body {
	width: 100%;
	height: 100%;
	overflow: hidden;
	margin: 0;
}

#container {
	width: calc(100% - 320px);
	height: 500px !important;
}
</style>