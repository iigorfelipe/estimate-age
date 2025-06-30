<script>
	import { createEventDispatcher } from 'svelte';

	export let name;
	export let age;

	const dispatch = createEventDispatcher();

	function handleClick() {
		dispatch('clear');
	}
</script>

<div class="container">
	<div class="result-card">
		<p class="result-card-name">{name}</p>
		<p class="result-card-age">{age} anos</p>
	</div>
	<button on:click={handleClick}>Tentar com outro nome</button>
</div>

<style>
	.container {
		display: flex;
		flex-direction: column;
		gap: 1rem;
	}

	.result-card {
		text-align: center;
		padding: 1.5rem;
		background: linear-gradient(to right, #ecfdf5, #f0fdfa);
		border-radius: 0.75rem;
		border: 1px solid var(--color-border);
		gap: 0.5rem;
		display: flex;
		flex-direction: column;
	}

	.result-card-name {
		color: #1f2937;
		font-size: 1.5rem;
		font-weight: 700;
	}

	.result-card-age {
		font-size: 2.25rem;
		font-weight: 700;
		background: linear-gradient(to right, var(--color-primary), var(--color-secondary));
		background-clip: text;
		color: transparent;
	}

	button {
		border-radius: 0.375rem;
		font-size: 0.875rem;
		height: 2.5rem;
		padding: 0.5rem 1rem;
		border: 1px solid var(--color-border);
		background-color: transparent;
		cursor: pointer;
		transition:
			background-color 0.2s ease,
			color 0.2s ease;
	}

	button:hover {
		background-color: #ecfdf5;
	}
</style>
