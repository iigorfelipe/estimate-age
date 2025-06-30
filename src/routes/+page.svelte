<script>
	import { tick, onDestroy } from 'svelte';
	import { navigating } from '$app/stores';
	import { goto, afterNavigate } from '$app/navigation';
	import MainHeader from '$lib/components/main-header.svelte';
	import ResultCard from '$lib/components/result-card.svelte';

	export let data;

	let inputValue = data.name;
	let isFocused = false;
	let shouldFocusInput = false;
	let inputRef;
	let debounceTimeout;

	const DEBOUNCE_DELAY = 800;

	function handleClear() {
		inputValue = '';
		shouldFocusInput = true;
		updateURLWithName('');
	}

	function updateURLWithName() {
		const params = new URLSearchParams(window.location.search);
		const validValue = inputValue.trim();

		if (validValue) {
			params.set('name', validValue);
		} else {
			params.delete('name');
		}
		goto(`/?${params.toString()}`, { replaceState: true });
	}

	function handleInput() {
		clearTimeout(debounceTimeout);

		debounceTimeout = setTimeout(() => {
			updateURLWithName();
		}, DEBOUNCE_DELAY);
	}

	afterNavigate(async () => {
		if (shouldFocusInput) {
			await tick();
			inputRef?.focus();
			shouldFocusInput = false;
		}
	});

	onDestroy(() => {
		clearTimeout(debounceTimeout);
	});
</script>

<div class="container">
	<main>
		<MainHeader />

		<div class="card">
			<span>Digite um nome e veja a estimativa aparecer automaticamente</span>

			<input
				type="text"
				placeholder={!isFocused ? 'Digite um nome...' : ''}
				bind:value={inputValue}
				on:input={handleInput}
				bind:this={inputRef}
				on:focus={() => (isFocused = true)}
				on:blur={() => (isFocused = false)}
			/>

			{#if inputValue}
				{#if $navigating}
					<div class="loading-container">
						<span>Estimando...</span>
					</div>
				{:else if data.age}
					<ResultCard name={inputValue} age={data.age} on:clear={handleClear} />
				{/if}
			{/if}
		</div>

		<span class="info-text"> 💡 Esta é apenas uma estimativa baseada em dados estatísticos </span>
	</main>
</div>

<style>
	.container {
		height: 100vh;
		display: flex;
		align-items: center;
		justify-content: center;
		background: linear-gradient(to bottom right, #ecfdf5, #f0fdfa, #f0fdf4);
	}

	main {
		display: flex;
		flex-direction: column;
		text-align: center;
		max-width: 28.125rem;
		gap: 1.5rem;
	}

	.card {
		border-radius: 0.5rem;
		box-shadow:
			0 20px 25px -5px rgba(0, 0, 0, 0.1),
			0 8px 10px -6px rgba(0, 0, 0, 0.1);
		border: 0;
		background-color: #fff;
		backdrop-filter: blur(4px);
		display: flex;
		flex-direction: column;
		padding: 1.5rem;
		gap: 1rem;
	}

	.card span {
		text-align: center;
		color: #71717a;
		font-size: 0.875rem;
	}

	main input {
		display: flex;
		height: 1.75rem;
		padding: 0.5rem 0.75rem;
		border-radius: 0.5rem;
		border: 1px solid #d1d5db;
		background-color: #fff;
		color: #111827;
		font-size: 1rem;
		text-align: center;
		transition:
			border-color 0.2s ease,
			box-shadow 0.2s ease;
	}

	main input::placeholder {
		color: #9ca3af;
	}

	main input:focus {
		outline: none;
		border-color: var(--color-primary);
		box-shadow: 0 0 0 1px var(--color-primary);
	}

	.info-text {
		text-align: center;
		color: #6b7280;
		font-size: 0.875rem;
	}

	.loading-container {
		min-height: 11.6875rem;
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.loading-container span {
		color: var(--color-primary);
		font-size: 1rem;
	}
</style>
