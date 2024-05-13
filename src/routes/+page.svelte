<script lang="ts">
	import Title from '$lib/components/title/title.svelte';
	import Task from '$lib/components/task/task.svelte';
	import AddTask from '$lib/components/addTask/addTask.svelte';
</script>

<main>
	<Title />
	<div class="min-h-screen flex justify-center">
		<div class="md:w-1/2 flex flex-col justify-center gap-4 px-4">
			<Task />
			<Task />
			<Task />
			<Task />
			<Task />
			<Task />
			<Task />
			<Task />
			<Task />
			<AddTask />
		</div>
	</div>
</main>
