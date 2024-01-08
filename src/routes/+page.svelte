<script>
	let todos = $state([]);
	let filter = $state('all');
	let filteredTodos = $derived(filterTodos());

	function addTodo(event) {
		if (event.key !== 'Enter') return;

		const todoElement = event.target;
		const text = todoElement.value;
		const checked = false;

		if (text !== '') {
			todos = [...todos, { text, checked }];
			todoElement.value = '';
		}
	}

	function editTodo(event) {
		const todoElement = event.target;
		const index = todoElement.dataset.index;
		todos[index].text = todoElement.value;
	}

	function toggleTodo(event) {
		const todoElement = event.target;
		const index = todoElement.dataset.index;
		todos[index].checked = !todos[index].checked;
	}

	function setFilter(selectedFilter) {
		filter = selectedFilter;
	}

	function filterTodos() {
		switch (filter) {
			case 'all':
				return todos;
			case 'remaining':
				return todos.filter((todo) => !todo.checked);
			case 'completed':
				return todos.filter((todo) => todo.checked);
		}
	}
</script>

<div class="todos h-screen w-screen flex flex-col justify-center items-center gap-4">
	{#each filteredTodos as todo, i}
		<div class="todo flex gap-2">
			<input
				type="text"
				value={todo.text}
				class="input input-bordered"
				oninput={editTodo}
				data-index={i}
			/>
			<div class="checkboxes flex justify-center items-center">
				<input
					type="checkbox"
					checked={todo.checked}
					class="checkbox checkbox-success"
					onchange={toggleTodo}
					data-index={i}
				/>
			</div>
		</div>
	{/each}
	<div class="flex">
		<input type="text" class="input input-bordered" placeholder="Add todo" onkeydown={addTodo} />
	</div>

	<div class="filters">
		{#each ['all', 'remaining', 'completed'] as filter}
			<button class="btn btn-outline mx-2" onclick={() => setFilter(filter)}>{filter}</button>
		{/each}
	</div>
</div>
