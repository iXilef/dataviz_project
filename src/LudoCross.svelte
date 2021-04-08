<script>
	let cross_points = [{x: 0, y: 1}, {x: 1, y: 1}, {x: 1, y: 0}, {x: 2, y: 0},{x: 2, y: 1},
		{x: 3, y: 1},{x: 3, y: 2},{x: 2, y: 2},{x: 2, y: 3},{x: 1, y: 3},{x: 1, y: 2},
		{x: 0, y: 2},{x: 0, y: 1}];

	import {scaleLinear} from 'd3-scale';

	export let cross_size;

	let scale_cross = scaleLinear().domain([0,3]).range([0,cross_size]);

	let rescale = function(points) {
		var i;
		var cross_points_str = "";
		for (i = 0; i < cross_points.length; i++) {
			cross_points_str += scale_cross(points[i].x).toString() + "," + scale_cross(points[i].y).toString() + " ";
		}
		return cross_points_str
	}
</script>

<g>
    <defs>
        <linearGradient id="MyGradient" x1="0%" x2="0%" y1="0%" y2="100%">
          <stop offset="5%" stop-color="green" />
          <stop offset="35%" stop-color="yellow" />
          <stop offset="65%" stop-color="yellow" />
          <stop offset="95%" stop-color="red" />
        </linearGradient>
    </defs>
    <polyline points={rescale(cross_points)} fill="url(#MyGradient)" stroke="black"/>
</g>