<script lang="ts">
	import X from 'lucide-svelte/icons/x';

	import { Input } from '$lib/components/ui/input/index.js';
	import { Checkbox } from '$lib/components/ui/checkbox';
	import { Button } from '$lib/components/ui/button/index.js';

	import Title from '$lib/components/title/title.svelte';

	interface Task {
		id: string;
		title: string;
		status: boolean;
	}

	let hasTasks: boolean = $state(false);
	let tasks: Task[] = $state([]);
	let loading: boolean = $state(true);

	$effect(() => {
		const fetchData = async () => {
			try {
				let storageData = await localStorage.getItem('tasks.');
				if (storageData && storageData != '[]') {
					tasks = JSON.parse(storageData);
					hasTasks = true;
					loading = false;
				} else {
					hasTasks = false;
					loading = false;
				}
			} catch (error) {
				console.error('Error fetching data from localStorage:', error);
			}
		};
		fetchData();
	});

	function addTask(event: KeyboardEvent) {
		if (event.key !== 'Enter') return;

		const inputElement = event.target as HTMLInputElement;
		if (!inputElement) {
			console.error('Input element not found.');
			return;
		}

		if (inputElement.value === '') {
			return;
		}

		const task = {
			id: window.crypto.randomUUID(),
			title: inputElement.value,
			status: false
		};

		let storageData = localStorage.getItem('tasks.');
		let parsedData = storageData ? JSON.parse(storageData) : [];
		parsedData.push(task);
		localStorage.setItem('tasks.', JSON.stringify(parsedData));

		hasTasks = true;
		tasks = JSON.parse(JSON.stringify(parsedData));
		inputElement.value = '';
	}

	function updateTask(event: KeyboardEvent) {
		if (event.key !== 'Enter') return;

		const inputElement = event.target as HTMLInputElement;
		if (!inputElement) {
			console.error('Input element not found.');
			return;
		}

		if (inputElement.value === '') {
			return;
		}

		const taskId = inputElement.dataset.uuid;
		if (taskId === undefined) {
			console.error(`Task with UUID ${taskId} not found.`);
			return;
		}
		let taskToUpdate = tasks.find((t: Task) => t.id === taskId);
		if (!taskToUpdate) {
			console.error(`Task with UUID ${taskId} not found.`);
			return;
		}
		taskToUpdate.title = inputElement.value;

		update(taskToUpdate, taskId);
		inputElement.blur();
	}

	function updateCheckStatus(checkbox: EventTarget & HTMLButtonElement) {
		const inputElement = checkbox.parentElement?.firstChild as HTMLInputElement;
		if (!inputElement) {
			console.error('Input element not found.');
			return;
		}

		const taskId = inputElement.dataset.uuid;
		if (taskId === undefined) {
			console.error(`Task with UUID ${taskId} not found.`);
			return;
		}

		let taskToUpdate = tasks.find((t: Task) => t.id === taskId);
		if (!taskToUpdate) {
			console.error(`Task with UUID ${taskId} not found.`);
			return;
		}
		taskToUpdate.status = checkbox.dataset.state === 'checked' ? false : true;
		update(taskToUpdate, taskId);
	}

	function update(taskToUpdate: Task, taskId: string) {
		let storageData = localStorage.getItem('tasks.');
		let parsedData = storageData ? JSON.parse(storageData) : [];
		const index = parsedData.findIndex((t: Task) => t.id === taskId);
		if (index > -1) {
			parsedData[index] = taskToUpdate;
		}
		localStorage.setItem('tasks.', JSON.stringify(parsedData));
	}

	function removeTask(event: Event) {
		const button = event.target as HTMLButtonElement;
		const inputElement = button.parentElement?.querySelector(
			'input[type="text"]'
		) as HTMLInputElement | null;
		if (!inputElement) {
			console.error('Input element not found.');
			return;
		}

		const taskId = inputElement.dataset.uuid;
		tasks = tasks.filter((task) => task.id !== taskId);

		if (tasks.length === 0) {
			hasTasks = false;
		}
		localStorage.setItem('tasks.', JSON.stringify(tasks));
	}

	function clearAllTasks() {
		let inputElement = document.querySelector<HTMLInputElement>('#main-input');
		if (!inputElement) {
			return;
		}

		inputElement.value = '';
		localStorage.removeItem('tasks.');
		hasTasks = false;
		tasks = [];
	}
</script>

<main>
	<Title />

	<div class="min-h-[90vh] flex justify-center items-center">
		{#if loading}
			<h1 class="text-xl">Loading...</h1>
		{:else}
			<div class="w-full md:w-1/2 flex flex-col justify-center gap-4 px-4">
				{#if hasTasks}
					{#each tasks as task}
						<div class="flex items-center gap-4">
							<Input data-uuid={task.id} type="text" value={task.title} onkeydown={updateTask} />
							<Checkbox
								checked={task.status}
								on:click={(e) => {
									updateCheckStatus(e.detail.currentTarget);
								}}
							/>
							<Button onclick={removeTask}><X class="h-6 w-6" /></Button>
						</div>
					{/each}
				{:else}
					<h1 class="text-xl">No tasks.</h1>
				{/if}
				<div class="flex items-center gap-4">
					<Input id="main-input" type="text" placeholder="Add task" onkeydown={addTask} />
				</div>
				{#if hasTasks}
					<Button class="mb-4" onclick={clearAllTasks}>Clear</Button>
				{/if}
			</div>
		{/if}
	</div>
</main>
