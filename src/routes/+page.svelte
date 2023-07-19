<script lang="ts">
	import Game from '$src/routes/Game.svelte';
	import Modal from '$src/routes/Modal.svelte';
	import { levels, type Level } from '$src/routes/levels';
	import { confetti } from '@neoconfetti/svelte';
	import '../styles.css';

	let state: 'waiting' | 'playing' | 'paused' | 'won' | 'lost' = 'waiting';

	let game: Game;
</script>

<Game
	bind:this={game}
	on:play={() => {
		state = 'playing';
	}}
	on:lose={() => {
		state = 'lost';
	}}
	on:win={() => {
		state = 'won';
	}}
	on:pause={() => {
		state = 'paused';
	}}
/>

{#if state !== 'playing'}
	<Modal>
		<header>
			<h1>e<span>match</span>i</h1>
			<p>the emoji matching game</p>
		</header>

		{#if state === 'won' || state === 'lost'}
			<p>you {state} the game!</p>
		{:else if state === 'paused'}
			<p>game paused</p>
		{:else if state === 'waiting'}
			<p>choose a level:</p>
		{/if}

		<div class="buttons">
			{#if state === 'paused'}
				<button
					on:click={() => {
						game.resume();
					}}>resume</button
				>
				<button
					on:click={() => {
						state = 'waiting';
						game.quit();
					}}>quit</button
				>
			{:else}
				{#each levels as level (level)}
					<button
						on:click={() => {
							game.start(level);
						}}>{level.label}</button
					>
				{/each}
			{/if}
		</div>
	</Modal>
{/if}

{#if state === 'won'}
	<div
		class="confetti"
		use:confetti={{
			stageHeight: innerHeight,
			stageWidth: innerWidth
		}}
	/>
{/if}

<style>
	h1 {
		font-size: 4em;
		font-weight: 700;
		margin: 0;
	}
	p {
		font-family: GrandStander;
		margin: 0;
		margin-bottom: 1em;
	}
	h1 span {
		color: purple;
	}
	.confetti {
		position: fixed;
		width: 100%;
		height: 100%;
		left: 50%;
		top: 30%;
		pointer-events: none;
	}
</style>
