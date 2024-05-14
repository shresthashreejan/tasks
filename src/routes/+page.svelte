<script lang="ts">
	import Title from '$lib/components/title/title.svelte';
	import Task from '$lib/components/task/task.svelte';
	import AddTask from '$lib/components/addTask/addTask.svelte';

	let hasTasks: boolean = $state(false);
	let tasks: [] = $state([]);
	$effect(() => {
		let storageData = localStorage.getItem('tasks.');
		if (!storageData || storageData == '[]') {
			hasTasks = false;
		} else {
			hasTasks = true;
			tasks = JSON.parse(storageData);
		}
	});
</script>

<main>
	<Title />
	<div class="min-h-screen flex justify-center">
		<div class="md:w-1/2 flex flex-col justify-center gap-4 px-4">
			{#if hasTasks}
				{#each tasks as task}
					<Task {task} />
				{/each}
			{:else}
				<h1>No tasks.</h1>
			{/if}
			<AddTask />
		</div>
	</div>
</main>
