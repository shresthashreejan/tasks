<script lang="ts">
	import { Input } from '$lib/components/ui/input/index.js';
	import { Checkbox } from '$lib/components/ui/checkbox';

	export let task;
</script>

<div class="flex items-center gap-4">
	<Input type="text" value={task.title} />
	<Checkbox />
</div>
