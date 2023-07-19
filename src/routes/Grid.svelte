<script lang="ts">
	import Square from '$src/routes/Square.svelte';
	import { createEventDispatcher } from 'svelte';

	export let grid: string[] = [];
	export let found: string[] = [];

	const dispatch = createEventDispatcher();

	export let a: number;
	export let b: number;
	let resetTimeout: number;

</script>

<div class="grid">
	{#each grid as emoji, i}
		<Square
			group={grid.indexOf(emoji) === i ? 'a' : 'b'}
			selected={a === i || b === i}
			found={found.includes(emoji)}
			{emoji}
			on:click={() => {
				clearTimeout(resetTimeout);
				if (a === -1 && b === -1) {
					a = i;
				} else if (b === -1) {
					b = i;
					if (grid[a] === grid[b]) {
						// correct
						dispatch('found', {
							emoji
						});
					} else {
						// incorrect
						resetTimeout = setTimeout(() => {
							a = b = -1;
						}, 500);
					}
				} else {
					b = -1;
					a = i;
				}
			}}
		/>
	{/each}
</div>

<style>
	.grid {
		display: grid;
		grid-template-columns: repeat(var(--size), 1fr);
		grid-template-rows: repeat(var(--size), 1fr);
		gap: 0.5em;
		height: 100%;
		perspective: 100vw;
	}
</style>
