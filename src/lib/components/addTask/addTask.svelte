<script lang="ts">
	import { Input } from '$lib/components/ui/input/index.js';
	import { Button } from '$lib/components/ui/button/index.js';

	function addTask(event: Event) {
		const button = event.target as HTMLButtonElement;
		const inputElement = button.parentElement?.querySelector(
			'input[type="text"]'
		) as HTMLInputElement | null;

		if (!inputElement) {
			console.error('Input element not found.');
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
	}
</script>

<div class="flex items-center gap-4">
	<Input type="text" placeholder="Add task" />
	<Button onclick={addTask}>Add</Button>
</div>
