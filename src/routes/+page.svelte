<script>
	import { fly } from 'svelte/transition';
	let tasks = $state([]);
	let remaining = $derived(remainingTasks());

	function addTask(event) {
		if (event.key !== 'Enter') return;
		const inputElement = event.target;
		const text = inputElement.value;
		const checked = false;

		if (text !== '') {
			tasks = [...tasks, { text, checked }];
			inputElement.value = '';
		}
	}

	function editTask(event) {
		const taskElement = event.target;
		const index = taskElement.dataset.index;
		tasks[index].text = taskElement.value;
	}

	function toggleTask(event) {
		const taskElement = event.target;
		const index = taskElement.dataset.index;
		tasks[index].checked = !tasks[index].checked;
	}

	function remainingTasks() {
		let remainingTasks = tasks.filter((task) => !task.checked);
		return remainingTasks.length;
	}

	function clearAllTasks() {
		tasks = [];
	}

	let currentDate = new Date();
	let day = currentDate.toLocaleString('en-US', { weekday: 'long' });
	let date = currentDate.toLocaleString('en-US', {
		day: 'numeric',
		month: 'numeric',
		year: 'numeric'
	});
</script>

<div class="tasks h-screen flex flex-col justify-center items-center gap-4">
	<div class="flex flex-col items-center justify-center text-5xl">
		<h1>{day}</h1>
		<h1>{date}</h1>
	</div>
	{#each tasks as task, i}
		<div class="task flex gap-2" in:fly={{ x: 100, duration: 200 }}>
			<input
				type="text"
				value={task.text}
				class="input input-bordered"
				oninput={editTask}
				data-index={i}
			/>
			<div class="checkboxes flex justify-center items-center">
				<input
					type="checkbox"
					checked={task.checked}
					class="checkbox checkbox-lg checkbox-success rounded-full"
					onchange={toggleTask}
					data-index={i}
				/>
			</div>
		</div>
	{/each}
	<div class="flex justify-center items-center">
		<input
			type="text"
			class=" w-full md:w-96 input input-bordered input-lg"
			placeholder="Add task"
			onkeydown={addTask}
		/>
	</div>
	{#if remaining > 0}
		<div>You have {remaining} tasks left.</div>
	{:else}
		<div>You have no tasks left.</div>
	{/if}

	{#if tasks.length > 0}
		<button class="btn btn-active" onclick={clearAllTasks}>clear all</button>
	{/if}
</div>
