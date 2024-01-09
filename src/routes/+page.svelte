<script>
	let tasks = $state([]);
	let remaining = $derived(remainingTasks());

	function addTask(event) {
		if (event.key !== 'Enter') return;
		const taskElement = event.target;
		const text = taskElement.value;
		const checked = false;

		if (text !== '') {
			tasks = [...tasks, { text, checked }];
			taskElement.value = '';
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

	function deleteTask(event) {
		const taskElement = event.target;
		const index =
			taskElement.parentElement.parentElement.parentElement.childNodes[2].childNodes[0].dataset
				.index;
		tasks.splice(index, 1);
	}
</script>

<div class="tasks h-screen w-screen flex flex-col justify-center items-center gap-4">
	{#each tasks as task, i}
		<div class="task flex gap-2">
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
			<div class="flex justify-center items-center">
				<button class="btn btn-sm btn-outline btn-error rounded-full" onclick={deleteTask}
					><svg
						xmlns="http://www.w3.org/2000/svg"
						class="h-6 w-6"
						fill="none"
						viewBox="0 0 24 24"
						stroke="currentColor"
						><path
							stroke-linecap="round"
							stroke-linejoin="round"
							stroke-width="2"
							d="M6 18L18 6M6 6l12 12"
						/></svg
					></button
				>
			</div>
		</div>
	{/each}
	<div class="flex w-screen px-14">
		<input
			type="text"
			class=" w-full input input-bordered"
			placeholder="Add task"
			onkeydown={addTask}
		/>
	</div>
	{#if remaining > 0}
		<div>You have {remaining} tasks left.</div>
	{:else}
		<div>You have no tasks left.</div>
	{/if}
</div>
