<script>
	import LudoCross from './LudoCross.svelte';
	import LudoPointGenerator from './LudoPointGenerator.svelte';
    import {scaleLinear} from 'd3-scale';
	import { each, missing_component, text } from 'svelte/internal';
	import { data_bayesaverage, data_owned, data_players, data_playtime, data_weight, data_count} from "./data/sorted.js";
	import Pion from "./Pion.svelte";
	
	let show_lines = false;
	let lower_slice = 0;
	let max_category = 80;
	let categories = data_count;
    let cross_size = 1000;
    let nr_points = 2;
	let point_radius = 30;
	$: slice = (nr_points * 3 - 2) * 4 + lower_slice;
	$: slider_max = (nr_points * 3 - 2) * 4 + lower_slice;
	let cross_points = [{x: 0, y: 1}, {x: 1, y: 1}, {x: 1, y: 0}, {x: 2, y: 0},{x: 2, y: 1},
		{x: 3, y: 1},{x: 3, y: 2},{x: 2, y: 2},{x: 2, y: 3},{x: 1, y: 3},{x: 1, y: 2},
		{x: 0, y: 2},{x: 0, y: 1}];
	let checkthis;
	let selected_datapoint = undefined;
	let mouse_x, mouse_y;
	let stroke_max = 30;
	let weight, bayesaverage, owned, year, firstgame, 	secondgame, thirdgame, categoryname, maxplayers, maxplaytime, count_cat;
	let firstcat, secondcat, thirdcat, firstweight, secondweight, thirdweight, firstcount, secondcount, thirdcount, firstmaxtime, secondmaxtime, thirdmaxtime;
	const setMousePosition = function(event) {
		mouse_x = event.clientX;
		mouse_y = event.clientY;
	}
	var i;



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

		for (i=0; i < positions.length; i++) {
		positions[i].x = rescale_points(positions[i].x)
		positions[i].y = rescale_points(positions[i].y)
	}

		return positions
	};

	$: data = generate_ludo_points(nr_points);


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
		console.log(point1, point2)
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
			// console.log(circlenum)
			if (xy === "x"){
				return data[circlenum].x
			} else {
				return data[circlenum].y
			}
		}
	}

	const count_max = function(category){
		let count = 0;
		for (let j in category.neighbours){
			count = count + category.neighbours[j].count
		}
		return count
	}

	const stroke_rescale = function(count, category) {
		// console.log(count_max(category),count, category.count)
		return ((count/category.count)*stroke_max)
	}

	const myscale = scaleLinear().domain([2,7129]).range([0,stroke_max]);

	const stroke_rescale2 = function(count){
		// console.log(count, myscale(count))
		return myscale(count)
	}

	const getinfo = function(category){
		weight = category.weight.toFixed(2);
		bayesaverage = category.bayesaverage.toFixed(2);
		owned = category.owned.toFixed(0);
		year = category.year;
		categoryname = category.maincat;
		maxplayers = category.maxplayers.toFixed(0);
		maxplaytime = category.maxplaytime.toFixed(0);
		count_cat = category.count;
		firstgame = category.neighbours[0].thumbnail;
		secondgame = category.neighbours[1].thumbnail;
		thirdgame = category.neighbours[2].thumbnail;
		firstcat = category.neighbours[0].catname;
		secondcat = category.neighbours[1].catname;
		thirdcat = category.neighbours[2].catname;
		firstweight = category.neighbours[0].weight.toFixed(0);
		secondweight = category.neighbours[1].weight.toFixed(0);
		thirdweight = category.neighbours[2].weight.toFixed(0);
		firstcount = category.neighbours[0].count;
		secondcount = category.neighbours[1].count;
		thirdcount = category.neighbours[2].count;
		firstmaxtime = category.neighbours[0].maxplaytime.toFixed(0);
		secondmaxtime = category.neighbours[1].maxplaytime.toFixed(0);
		thirdmaxtime = category.neighbours[2].maxplaytime.toFixed(0);
		}


	function onClickHandler(e){
		checkthis = e
	}

	// function update_points(){
	// 	lower_slice = 0
	// }

	function dataHandler(data) {
		categories = data
	}

	function logging(data1, data2, data3){
		console.log(data1, data2, data3)
	}

</script>

<style>

	svg {
		background-color: whitesmoke;
		padding: 10px;
		box-shadow: 5px;
	}
	.outercircle{
		fill: steelblue;
		cursor: pointer;
	}
	.circle_text{
		font-size: small;
		fill: rgb(47, 14, 78);
		font-weight: 700;
	}
	.all_paths{
		opacity: 0.1;
	}
	.all_paths:hover{
		opacity: 1;
	}
	.click_path{
		stroke: blue;
	}
	.column {
		/* float:left;
		width: 20%;
		position: absolute; */
		position: absolute;
		left: 0px;
		top: 50px;

	}
	#tooltip {
		position: fixed;
		background-color: white;
		border-color: red;
		border-width: 2px;
		border-style: solid;
  	}
	#right_side {
		position: absolute;
		top: 60px;
		left: 700px;
		white-space: nowrap;
		border-color: red;
		border-width: 10px;
		border-style: solid;
	}
	.dropbtn {
		background-color: #04AA6D;
		color: white;
		padding: 16px;
		font-size: 16px;
		border: none;
		cursor: pointer;
    }
    
    .dropdown {
		position: relative;
		display: inline-block;
    }
	th {
		border: 1px solid black;
		border-collapse: collapse;
		padding: 10px;
		text-align: left;
	}
    
    /* .dropdown-content {
		display: none;
		position: absolute;
		right: 0;
		background-color: whitesmoke;
		min-width: 100px;
		box-shadow: 0px 8px 16px 0px rgba(0,0,0,0.2);
		z-index: 1;
    } */

	.databtn {
		background-color: steelblue;
		color: white;
		padding: 16px;
		font-size: 16px;
		border: none;
		cursor: pointer;
	}
    .dropdown-content button:hover {background-color: darkblue;}
    .dropdown:hover .dropdown-content {display: block;}
    .dropdown:hover .dropbtn {background-color: #3e8e41;}

</style>
<!-- DROPDOWN MENU DIV -->
<div class="dropdown" style="float:left;">
	<button on:click={() => {dataHandler(data_bayesaverage)}} class="databtn">Rating</button>
	<button on:click={() => {dataHandler(data_weight)}} class="databtn">Weight</button>
	<button on:click={() => {dataHandler(data_playtime)}} class="databtn">Playtime</button>
	<button on:click={() => {dataHandler(data_players)}} class="databtn">Players</button>
	<button on:click={() => {dataHandler(data_owned)}} class="databtn">Owned</button>
	<button on:click={() => {dataHandler(data_count)}} class="databtn">Count</button>
    <!-- <div class="dropdown-content" style="left:0;">
		<button on:click={() => {dataHandler("Rating")}} class="databtn">Rating</button>
		<button on:click={() => {dataHandler("Weight")}} class="databtn">Weight</button>
		<button on:click={() => {dataHandler("Playtime")}} class="databtn">Playtime</button>
		<button on:click={() => {dataHandler("Players")}} class="databtn">Players</button>
    </div> -->
</div>

<div class="slidecontainer">
	<input name="dafdaf" type="range" min="1" max="8" bind:value={nr_points} class="slider" id="myRange">
	<input type="range" min="0" max="80" bind:value={lower_slice} class="slider" id="slice">
	{((nr_points * 3 - 2)*4)} Categories are displayed. {nr_points} per side are selected. Category #{lower_slice} to Category #{(slider_max < 85)? slider_max: 84} is being displayed.
	<input type="checkbox" bind:checked={show_lines}> Show all connections
</div>
<div class="row">
<div class="column">
<svg width={cross_size} height={cross_size}>
	<LudoCross cross_size = {cross_size}></LudoCross>

	{#each categories.slice(lower_slice, slice) as category, i}
		
		{#each category.neighbours as neighbour,j}
		{#if neighbour.index >=lower_slice && neighbour.index < slice}
		{#if checkthis == category.maincat}
		<path class = "click_path" d={make_path(data[i], data[neighbour.index-lower_slice])} style="fill: transparent; stroke-width: {stroke_rescale2(neighbour.count)};"/>
		{/if}
		{#if show_lines}
		<path class = "all_paths" d={make_path(data[i], data[neighbour.index-lower_slice])} fill = "transparent" stroke="black"/>
		{/if}
		{/if}
		{/each}
		
	{/each}

	<!-- {#each categories.slice(lower_slice, slice) as category, i}	
		{#if show_lines}	
		{#each category.neighbours as neighbour, j}
			{#if neighbour.index >= lower_slice && neighbour.index <= slice}	
			<path class = "all_paths" d={make_path(data[i], data[neighbour.index-lower_slice])} fill = "transparent" stroke="black"/>
			{/if}
		{/each}
		{/if}
		{#if checkthis == category.maincat}
			{#each category.neighbours as neighbour, j}
				{#if neighbour.index >= lower_slice && neighbour.index <= slice}		
					<path class = "click_path" d={make_path(data[i], data[neighbour.index-lower_slice])} style="fill: transparent; stroke-width: {stroke_rescale2(neighbour.count)};"/>
				{/if}
			{/each}
		{/if}	
	{/each} -->
	
	{#each categories.slice(lower_slice, slice) as category, i}
		<circle class = "outercircle" on:mouseover={(event) => {selected_datapoint = category.maincat; setMousePosition(event); getinfo(category); onClickHandler(category.maincat)}} 
		on:click={()  => onClickHandler(category.maincat)} x={data[i].x} y={data[i].y} 
		cx={data[i].x} cy={data[i].y} r={point_radius}/>
		<!-- svelte-ignore component-name-lowercase -->
		<text class = "circle_text" on:mouseover={(event) => {selected_datapoint = category.maincat; setMousePosition(event)}}
		on:mouseout = {() => {selected_datapoint = undefined}} 
		on:click={()  => onClickHandler(category.maincat)} x={data[i].x} y={data[i].y}
		text-anchor="middle" alignment-baseline="middle">{category.maincat}</text>
	{/each}
</svg>
</div>
{#if selected_datapoint != undefined}
	<div id="tooltip" style="left: {mouse_x + 10}px; top: {mouse_y - 10}px">
		Category: {selected_datapoint}
</div>
<div class = "column" id="details">
	<svg id ="svg" viewbox="0 0 100 100" width=	300px height=100px>
		<Pion x=50 head={weight*3} height={bayesaverage*10} width={bayesaverage*10}/>
	</svg>
	<div>
		Properties of category {categoryname}:
		<ul>
			<li>owned: {owned}</li>
			<li>bayesaverage: {bayesaverage}</li>
			<li>weight: {weight}</li>
			<li>year: {year}</li>
			<li>maximal players: {maxplayers}</li>
			<li>maximal playtime: {maxplaytime}</li>
			<li>count: {count_cat}</li>
		</ul>
		
	</div>
</div>
<div id="right_side">
	<div>
		<table style="width:100%">
			<tr>
				<th>Variable</th>				
			  <th>{firstcat}</th>
			  <th>{secondcat}</th> 
			  <th>{thirdcat}</th>
			</tr>
			<tr>
				<td>Top Games</td>				
			  <td><img src={firstgame}/></td>
			  <td><img src={secondgame}/></td> 
			  <td><img src={thirdgame}/></td>
			</tr>
			<tr>
				<td>Count</td>
			  <td>{firstcount}</td>
			  <td>{secondcount}</td>
			  <td>{thirdcount}</td>
			</tr>
			<tr>
				<td>Weight</td>
			  <td>{firstweight}</td>
			  <td>{secondweight}</td>
			  <td>{thirdweight}</td>
			</tr>
			<tr>
				<td>Playtime</td>
			  <td>{firstmaxtime}</td>
			  <td>{secondmaxtime}</td>
			  <td>{thirdmaxtime}</td>
			</tr>
		  </table>
	</div>
</div>
{/if}
</div>