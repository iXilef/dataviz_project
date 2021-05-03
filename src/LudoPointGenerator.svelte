<script>
    import {scaleLinear} from 'd3-scale';
	import { missing_component } from 'svelte/internal';

    export let cross_size;
    export let nr_points;
	export let point_radius;

	export let rescale_points = scaleLinear().domain([0,3]).range([0,cross_size]);

	export let generate_ludo_points = function(nr_points) {
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

	export const data = generate_ludo_points(nr_points);
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
</script>

<g>
    {#each data as point}
		<circle cx={point.x} cy={point.y} r={point_radius} />
	{/each}
	<!-- <path d={make_path(data[0], data[1])} fill = "transparent" stroke="black"/>
	<path d={make_path(data[0], data[16])} fill = "transparent" stroke="black"/>
	<path d={make_path(data[0], data[30])} fill = "transparent" stroke="black"/>
	<path d={make_path(data[0], data[47])} fill = "transparent" stroke="black"/>
	<path d={make_path(data[0], data[19])} fill = "transparent" stroke="black"/>
	<path d={make_path(data[0], data[51])} fill = "transparent" stroke="black"/> -->
</g>