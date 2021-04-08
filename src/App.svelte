<script>
	import { categories, categories_try, myCircles } from "./data/data.js";
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
	const line_show = function(){
		document.getElementById("lines").style.visibility = "visible";
	}

	const radius_max = 20;
</script>
<svg height=500 width=500>
{#each categories as category, i}
	<circle cx={myCircles[i][i].x} cy={myCircles[i][i].y} r={radius_max} class = "outercircle" on:click={line_show()}/>
	<circle cx={myCircles[i][i].x} cy={myCircles[i][i].y} r={radius_rescale(category.maincat_count, radius_max, category)} class = "innercircle"/>
	{#each category.neighbours as neighbour}
		<line id="lines" x1={myCircles[i][i].x} y1={myCircles[i][i].y} x2={get_line_coord(neighbour.cat, "x")} y2={get_line_coord(neighbour.cat, "y")} style="stroke-width: {neighbour.count};"/>
	{/each}
	<text x={myCircles[i][i].x} y={myCircles[i][i].y}>{category.maincat}</text>
{/each}
</svg>

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
		opacity: 0;
	}
	line:hover {
		opacity: 1;
	}
</style>
