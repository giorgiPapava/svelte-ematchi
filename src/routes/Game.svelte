<script lang="ts">
	import Countdown from '$src/routes/Countdown.svelte';
	import Found from '$src/routes/Found.svelte';
	import Grid from '$src/routes/Grid.svelte';
	import type { Level } from '$src/routes/levels';
	import { shuffle } from '$src/routes/utils';
	import { createEventDispatcher } from 'svelte';

	const dispatch = createEventDispatcher();

	let size: number;
	let grid: string[];
	let found: string[] = [];
	let remaining: number;
	let duration: number;
	let playing: boolean = false;

	let a: number = -1;
	let b: number = -1;

	export function start(level: Level) {
		size = level.size;
		grid = createGrid(level);
		remaining = duration = level.duration;

		resume();
	}

	export function quit() {
		a = b = -1;
		size = 0;
		grid = found = [];
		remaining = duration = 0;
		playing = false;
	}

	export function resume() {
		playing = true;
		countdown();
		dispatch('play');
	}

	function createGrid(level: Level) {
		const copy = level.emojis.slice();
		const pairs: string[] = [];

		for (let i = 0; i < level.size ** 2 / 2; i += 1) {
			const index = Math.floor(Math.random() * copy.length);
			const emoji = copy[index];

			copy.splice(index, 1);
			pairs.push(emoji);
		}

		pairs.push(...pairs);

		return shuffle(pairs);
	}

	function countdown() {
		const start = Date.now();
		let remainingAtStart = remaining;

		function loop() {
			if (!playing) return;
			requestAnimationFrame(loop);

			remaining = remainingAtStart - (Date.now() - start);

			if (remaining < 0) {
				dispatch('lose');
				playing = false;
			}
		}

		loop();
	}
</script>

<div class="game" style="--size: {size}">
	<div class="info">
		{#if playing}
			<Countdown
				on:click={() => {
					playing = false;
					dispatch('pause');
				}}
				{remaining}
				{duration}
			/>
		{/if}
	</div>

	<div class="grid-container">
		<Grid
			{a}
			{b}
			{grid}
			{found}
			on:found={(e) => {
				found = [...found, e.detail.emoji];

				if (found.length === (size * size) / 2) {
					quit();
					dispatch('win');
				}
			}}
		/>
	</div>

	<div class="info">
		<Found {found} />
	</div>
</div>

<style>
	.game {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		height: 100%;
		font-size: min(1vmin, 0.3rem);
	}

	.info {
		width: 80em;
		height: 10em;
	}

	.grid-container {
		width: 80em;
		height: 80em;
	}
</style>
