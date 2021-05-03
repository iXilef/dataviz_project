<script>
	import LudoCross from './LudoCross.svelte';
	import LudoPointGenerator from './LudoPointGenerator.svelte';
    import {scaleLinear} from 'd3-scale';
	import { missing_component } from 'svelte/internal';
	import { categories} from "./data/categories.js";

    let cross_size = 1000;
    let nr_points = 4;
	let point_radius = 30;
	let slice = (nr_points * 3 - 2) * 4
	let cross_points = [{x: 0, y: 1}, {x: 1, y: 1}, {x: 1, y: 0}, {x: 2, y: 0},{x: 2, y: 1},
		{x: 3, y: 1},{x: 3, y: 2},{x: 2, y: 2},{x: 2, y: 3},{x: 1, y: 3},{x: 1, y: 2},
		{x: 0, y: 2},{x: 0, y: 1}];
	let checkthis;

	let rescale_points = scaleLinear().domain([0,3]).range([0,cross_size]);

	let generate_ludo_points = function(nr_points) {
		let positions = [];
		var i;
		var j;
		var shift = 1/(2 * nr_points)
		for (i=0; i < nr_points; i++) {
			var y = shift + i/nr_points;
			for (j=0; j < nr_points; j++) {
				var x = shift + 1 + j/nr_points
				if (((j == 0) | (j == nr_points - 1)) | (i == 0)){
					positions.push({x: x, y: y})
				}
			}
		}
		for (i=0; i < nr_points; i++) {
			var y = shift + 1 + i/nr_points;
			for (j=0; j < nr_points; j++) {
				var x1 = shift +j/nr_points
				if (((i == 0) | (i == nr_points - 1)) | (j == 0)) {
					positions.push({x: x1, y: y})
				}
			}
			for (j=0; j < nr_points; j++) {
				var x2 = shift + 2 + j/nr_points
				if (((i == 0) | (i == nr_points - 1)) | (j == nr_points - 1)) {
					positions.push({x: x2, y: y})
				}
			}
		}
		for (i=0; i < nr_points; i++) {
			var y = shift + 2 + i/nr_points;
			for (j=0; j < nr_points; j++) {
				var x = shift + 1 + j/nr_points
				if (((j == 0) | (j == nr_points - 1)) | (i == nr_points-1)) {
					positions.push({x: x, y: y})
				}
			}
		}
		return positions
	};

	let data = generate_ludo_points(nr_points);
	var i;
	for (i=0; i < data.length; i++) {
		data[i].x = rescale_points(data[i].x)
		data[i].y = rescale_points(data[i].y)
	}

	let control = function(point1, point2) {
		var diff_x, diff_y, x1, x2, y1, y2;
		diff_x = point1.x - point2.x
		diff_y = point1.y - point2.y
		if (diff_x < 0) {x1 = 1.25} else {x1 = 1.75}
		if (diff_y < 0) {y1 = 1.25} else {y1 = 1.75}
		x2 = 3-x1;
		y2 = 3-y1;
		return [{x: rescale_points(x1), y: rescale_points(y1)},
				{x: rescale_points(x2), y: rescale_points(y2)}]
	}

	let make_path = function(point1, point2) {
		var controls = control(point1, point2)
		return "M " + point1.x + "," + point1.y +
		" C " + controls[0].x + "," + controls[0].y + 
		" " + controls[1].x + "," + controls[1].y + 
		" " + point2.x + "," + point2.y
	}
	let rescale = function(points) {
		var i;
		var cross_points_str = "";
		for (i = 0; i < cross_points.length; i++) {
			cross_points_str += scale_cross(points[i].x).toString() + "," + scale_cross(points[i].y).toString() + " ";
		}
		return cross_points_str
	}
	let scale_cross = scaleLinear().domain([0,3]).range([0,cross_size]);

	const get_line_coord = function(circlenum, xy) {
		if (circlenum != null){
			console.log(circlenum)
			if (xy === "x"){
				return data[circlenum].x
			} else {
				return data[circlenum].y
			}
		}
	}

	function onClickHandler(e){
		// console.log(e);
		checkthis = e
	}
</script>

<style>

	svg {
		background-color: whitesmoke;
		padding: 10px;
		box-shadow: 5px;
	}
	line {
		stroke: black;
		opacity: 1;
	}
	.outercircle{
		fill: steelblue;
		cursor: pointer;
	}
	.circle_text{
		font-size: small;
		fill: white;
	}
	.texting {
		max-width: 30;
	}
	.all_paths{
		opacity: 0.1;
	}
	.all_paths:hover{
		opacity: 1;
	}
	.click_path{
		stroke: blue;
		stroke-width: 5px;
	}
</style>



<svg width=1000 height=1000>
	<LudoCross cross_size = 1000></LudoCross>

	{#each categories.slice(0, slice) as category, i}		
		{#each category.neighbours.slice(0, slice) as neighbour, j}
			{#if neighbour.index < slice}	
			<path class = "all_paths" d={make_path(data[i], data[neighbour.index])} fill = "transparent" stroke="black"/>
			{/if}
		{/each}

		{#if checkthis == category.maincat}
			{#each category.neighbours.slice(0, slice) as neighbour, j}
				{#if neighbour.index < slice}	
					<path class = "click_path" d={make_path(data[i], data[neighbour.index])} fill = "transparent" stroke="black"/>
				{/if}
			{/each}
		{/if}	
	{/each}
	{#each categories.slice(0, slice) as category, i}
		<circle class = "outercircle" on:click={()  => onClickHandler(category.maincat)} cx={data[i].x} cy={data[i].y} r={point_radius}/>
		<text class = "circle_text" on:click={()  => onClickHandler(category.maincat)} x={data[i].x} y={data[i].y} text-anchor="middle" alignment-baseline="middle">{category.maincat}</text>
	{/each}
</svg>

