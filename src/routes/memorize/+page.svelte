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

	function blankMoreWords() {
		numberToHide++;
		while (hiddenIndices.length < numberToHide && hiddenIndices.length < numberOfWords) {
			let randomIndex = Math.floor(Math.random() * numberOfWords);
			if (!hiddenIndices.includes(randomIndex)) {
				hiddenIndices = [...hiddenIndices, randomIndex];
			}
		}
	}

	function blankFewerWords() {
		if (numberToHide > 0) {
			numberToHide--;
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
			<div class="button-container">
				<button class="show-button" onclick={() => (show = false)}>Hide words</button>
			</div>
		{:else}
			<p>
				{words.map((word, index) => getDisplayWord(word, index)).join(' ')}
			</p>
			<div class="button-container">
				<button onclick={() => (show = true)}>Show hidden words</button>
				<button onclick={() => (showFirstLetterOnly = !showFirstLetterOnly)}>
					{showFirstLetterOnly ? 'Hide all letters' : 'Show first letters'}
				</button>
			</div>
		{/if}
	</div>

	<div class="button-container">
		<button onclick={() => goto('/')}>Back</button>
		<button onclick={blankMoreWords} disabled={numberToHide===numberOfWords}>Hide more words</button>
		<button onclick={blankFewerWords} disabled={numberToHide === 0}>Hide fewer words</button>
	</div>
</div>

<!-- 
Hide/unhide words - done
show first letter - done
Todo: cut buttons down?
 -->

<!-- Voice input idea
 find good speach to text api figure out data output
 build type input first scripture typer style so i have the match system and ui working
 connect the two
 -->

<style>
	.memory-page {
		min-height: 100vh;
		background: linear-gradient(to bottom right, #f8f9ff, #e6f0ff);
		padding: 2rem;
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
		color: #2d3748;
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
