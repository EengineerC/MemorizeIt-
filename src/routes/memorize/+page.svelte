<script lang="ts">
	import { goto } from '$app/navigation';
	import Header from '../Header.svelte';
	import { inputText } from '../state.svelte';

	let numberToHide = $state(0);
	let show = $state(false);
	let showFirstLetterOnly = $state(false);

	let text = inputText.text;
	const words = text.split(' ');
	let hiddenIndices: number[] = $state([]);

	const numberOfWords = words.length;

	function updateHiddenWords() {
		while (hiddenIndices.length < numberToHide && hiddenIndices.length < numberOfWords) {
			let randomIndex = Math.floor(Math.random() * numberOfWords);
			if (!hiddenIndices.includes(randomIndex)) {
				hiddenIndices = [...hiddenIndices, randomIndex];
			}
		}
		while (hiddenIndices.length > numberToHide ) {
			hiddenIndices = hiddenIndices.slice(0, numberToHide);
		}
	}

	function getDisplayWord(word: string, index: number) {
		if (!hiddenIndices.includes(index)) {
			return word;
		}

		if (showFirstLetterOnly) {
			return word[0] + '_'.repeat(word.length - 1);
		}

		return '_'.repeat(word.length);
	}
</script>

<div class="memory-page">
	<Header />

	<div class="verse-container">
		{#if show}
			<p>{words.join(' ')}</p>
		{:else}
			<p>
				{words.map((word, index) => getDisplayWord(word, index)).join(' ')}
			</p>
		{/if}
		<div class="slider-container">
			<input
				type="range"
				id="hide-slider"
				min="0"
				max={numberOfWords}
				bind:value={numberToHide}
				oninput={updateHiddenWords}
				class="slider"
			/>
			<label for="hide-slider">Hide {numberToHide} words</label>
		</div>
	</div>
	<div class="button-container">
		<button onclick={() => goto('/')}>Back</button>
		<button onclick={() => (show = !show)}>{show ? 'Blank hidden words' : 'Show hidden words'}</button>
		<button onclick={() => (showFirstLetterOnly = !showFirstLetterOnly)}>
			{showFirstLetterOnly ? 'Hide all letters' : 'Show first letters'}
		</button>

	</div>
</div>

<style>
	.memory-page {
		padding-top: 7rem;
		min-height: 100vh;
		background: linear-gradient(to bottom right, #f8f9ff, #e6f0ff);
	}

	.verse-container {
		max-width: 800px;
		margin: 2rem auto;
		padding: 2rem;
		background: white;
		border-radius: 10px;
		box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
	}

	.button-container {
		max-width: 800px;
		margin: 2rem auto;
		display: flex;
		flex-wrap: wrap;
		justify-content: center;
		gap: 1rem;
	}

	.slider-container {
		max-width: 800px;
		margin: 2rem auto;
		display: flex;
		flex-direction: column;
		align-items: center;
		color: #606060
	}

	.slider {
		width: 80%;
		margin: 1rem 0;
		accent-color: #2c5282;
	}

	p {
		font-size: 1.4rem;
		line-height: 1.8;
		margin: 2rem 0;
		font-family:
			system-ui,
			-apple-system,
			sans-serif;
		white-space: pre-wrap;
		word-spacing: 0.5rem;
		color: #606060;
	}

	button {
		background-color: #2c5282;
		border: none;
		color: white;
		padding: 0.75rem 1.5rem;
		text-align: center;
		text-decoration: none;
		display: inline-block;
		font-size: 1rem;
		cursor: pointer;
		border-radius: 8px;
		transition: all 0.3s ease;
		box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
	}

	button:hover {
		background-color: #3182ce;
		transform: translateY(-1px);
	}
</style>
