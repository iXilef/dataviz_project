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



	function line_show2(){
		lines.array.forEach(element => {
			element.style.display = 'none'
		});
	}
	// const line_show = function(){
	// 	const lines = document.getElementsByTagName("lines");
	// 	lines.forEach(e => e.style.display= 'none');
	// }



	const radius_max = 20;
</script>
<svg height=500 width=500>
{#each categories as category, i}

	<circle cx={myCircles[i][i].x} cy={myCircles[i][i].y} r={radius_max} class = "outercircle" id = {category.maincat} on:click={line_show2}/>
	<circle cx={myCircles[i][i].x} cy={myCircles[i][i].y} r={radius_rescale(category.maincat_count, radius_max, category)} class = "innercircle"/>
	<text x={myCircles[i][i].x} y={myCircles[i][i].y}>{category.maincat}</text>
	{#each category.neighbours as neighbour, j}
		<line class="lines" href={category.maincat} bind:this={lines[j]} x1={myCircles[i][i].x} y1={myCircles[i][i].y} x2={get_line_coord(neighbour.cat, "x")} y2={get_line_coord(neighbour.cat, "y")} style="stroke-width: {neighbour.count};"/>
	{/each}
{/each}
</svg>
<button on:click={line_show2}>Hier klicken</button>
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
