<script>
    export let count_max = function(category){
        let count = 0
        let str = "_count"
        for (let j in category){
            if (j.search("_count") != -1){
                count = count + category[j]			
                }
        }
        return count
	}

	export const radius_rescale = function(radius_max, count, category) {
		return (count/count_max(category))*radius_max
	}
</script>