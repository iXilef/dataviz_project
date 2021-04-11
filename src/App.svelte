<script>
	import { each } from 'svelte/internal';
import { ludocircles } from './data/data.js';
</script>

<svg height = 1000 width= 1000>
	{#each ludocircles as circle, i}
	<circle cy={circle.y} cx = {circle.x} r=40 class = "outercircle"/>
	<text x={circle.x} y={circle.y} id="text">{i}</text>
	{/each}
</svg>

<style>
	svg {
		background-color: green;
	}
	#text {
		color: black;
	}
	circle {
		background-color: yellow;
	}
	.outercircle {
		fill: yellow;
	}
</style>