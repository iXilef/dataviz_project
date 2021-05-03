<script>
	import {myCircles } from "./data/data.js";
	import {categories} from "./data/categories.js";

	// import radius_rescale from "./scaling_functions.svelte";
	// console.log(categories[0].neighbours[0].cat, myCircles[0]);

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
			// console.log(circlenum)
			if (xy === "x"){
				return myCircles[circlenum][circlenum].x
			} else {
				return myCircles[circlenum][circlenum].y
			}
		}
	}
	const radius_max = 20;
	let checker = "no";
	let color;
	let opacity;
	let e;
	let checkthis;

	function onClickHandler(e){
		// console.log(e);
		checkthis = e
	}

</script>



<svg height=500 width=500>
	{#each categories.slice(1,3) as category, i}
	
		<circle on:click={()  => onClickHandler(category.maincat)} cx={myCircles[i][i].x} cy={myCircles[i][i].y} r={radius_max} class = "outercircle" id = {category.maincat}/>
		<!-- <circle cx={myCircles[i][i].x} cy={myCircles[i][i].y} r={radius_rescale(category.maincat_count, radius_max, category)} class = "innercircle"/> -->
		<text x={myCircles[i][i].x} y={myCircles[i][i].y}>{category.maincat}</text>


		<!-- {#each category.neighbours.slice(1,2) as neighbour, j}

			{#if checkthis == category.maincat}
			<line href={category.maincat} id={category.maincat} x1={myCircles[i][i].x} y1={myCircles[i][i].y} x2={get_line_coord(neighbour.cat, "x")} y2={get_line_coord(neighbour.cat, "y")} style="stroke-width: {neighbour.count};"/>
			{/if}

		{/each} -->

	{/each}
	{#if checker === "yes"}
		<circle cx=400 cy=400 r={radius_max} class = "outercircle" fill="yellow"/>
	{/if}
	</svg>

<button on:click={onClickHandler} id = "diesesding">Hier klicken</button>




<!-- <svg height=500 width=500>
	{#each categories as category, i}
	<circle cx={myCircles[i][i].x} cy={myCircles[i][i].y} r={radius_max} class = "outercircle" id = {category.maincat} on:click={()  => onClickHandler(category.maincat)}/>
	

	{/each}
</svg> -->
{#each categories.slice(1,3) as category, i}
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
		opacity: 0.3;
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
