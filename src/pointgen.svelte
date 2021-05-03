<script>
    import {scaleLinear} from 'd3-scale';

    export let cross_size;
    export let nr_points;
	export let point_radius;

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
</script>

<g>
    {#each data as point}
		<circle cx={rescale_points(point.x)} cy={rescale_points(point.y)} r={point_radius} />
	{/each}
</g>
