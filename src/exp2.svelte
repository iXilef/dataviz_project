<script>
	import { categories, myCircles } from "./data/data.js";
	// import radius_rescale from "./scaling_functions.svelte";
	console.log(categories[0].neighbours[0].cat, myCircles[0]);

    let count_max = function(category){
        let count = 0;
        let str = "_count";
        for (let j in category){
            if (j.search("_count") != -1){
                count = count + category[j];			
                }
        }
        return count
	}

	const radius_rescale = function(radius_max, count, category) {
		return (count/count_max(category))*radius_max
	}

	const get_line_coord = function(circlenum, xy) {
		if (circlenum != null){
			console.log(circlenum)
			if (xy === "x"){
				return myCircles[circlenum][circlenum].x
			} else {
				return myCircles[circlenum][circlenum].y
			}
		}
	}

let lines = [];
let color = "red";
let opacity = 1;
let checker = "no"


	function MouseInCircle(){
		color = "black";
		opacity = 1;
	}
	function MouseOutCircle(){
		color = "red";
		opacity = 1;
	}
	// const line_show = function(){
	// 	const lines = document.getElementsByTagName("lines");
	// 	lines.forEach(e => e.style.display= 'none');
	// }
	let id_variable;
	let connection;
	function onClickHandler(){

		if (checker === "yes"){
			checker = "no"
		}
		else {
			checker = "yes"
		}
	}

	const radius_max = 20;
</script>
<svg height=500 width=500>
{#each categories as category, i}

	<circle cx={myCircles[i][i].x} cy={myCircles[i][i].y} r={radius_max} class = "outercircle" id = {category.maincat} on:mouseover={MouseInCircle} on:mouseout={MouseOutCircle}/>
	<circle cx={myCircles[i][i].x} cy={myCircles[i][i].y} r={radius_rescale(category.maincat_count, radius_max, category)} class = "innercircle"/>
	<text x={myCircles[i][i].x} y={myCircles[i][i].y}>{category.maincat}</text>
	{#if checker ==="yes"}
	{#each category.neighbours as neighbour, j}
		<circle class="overlaycircle" id={category.maincat} cy = {get_line_coord(neighbour.cat, "y")} cx = {get_line_coord(neighbour.cat, "x")} r={radius_max} fill={color} opacity={opacity}/>
		<line id={category.maincat} x1={myCircles[i][i].x} y1={myCircles[i][i].y} x2={get_line_coord(neighbour.cat, "x")} y2={get_line_coord(neighbour.cat, "y")} style="stroke-width: {neighbour.count};"/>
	{/each}
	{/if}
{/each}
{#if checker === "yes"}
	<circle cx=400 cy=400 r={radius_max} class = "outercircle" on:mouseover={MouseInCircle} on:mouseout={MouseOutCircle} fill="yellow"/>
{/if}
</svg>
<button on:click={onClickHandler} id = "diesesding">Hier klicken</button>
{#each categories as category, i}
	<li>{category.maincat} - {category.maincat_count}- {i}</li>
{/each}

<style>
	svg {
		background-color: seagreen;
	}
	.outercircle {
		fill: red;
		cursor: pointer;
	}
	.innercircle {
		fill: gray;
		cursor: pointer;
	}
	line {
		stroke: greenyellow;
		opacity: 1;
	}
	#cat1:hover ~ [href="cat1"]{
		opacity: 1;
	}
	#cat2:hover ~ [href="cat2"]{
		opacity: 1;
	}
	#cat3:hover ~ [href="cat3"]{
		opacity: 1;
	}
</style>
