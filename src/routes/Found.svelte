<script lang="ts">
	import { receive } from '$src/routes/transitions';
	import { getTweemojiUrl } from '$src/routes/utils';

	export let found: string[];
</script>

<div class="found">
	{#each found as emoji (emoji)}
		<div class="pair">
			<img src={getTweemojiUrl(emoji)} alt={emoji} in:receive={{ key: `${emoji}_a` }} />
			<img src={getTweemojiUrl(emoji)} alt={emoji} in:receive={{ key: `${emoji}_b` }} />
		</div>
	{/each}
</div>

<style>
	.found {
		display: flex;
		justify-content: center;
		align-items: center;
		gap: 2em;
		height: 100%;
		filter: drop-shadow(0.2em 0.2em 0.5em rgba(0, 0, 0, 0.2));
	}

	.pair {
		display: flex;
		justify-content: center;
		align-items: center;
		width: 8em;
		height: 8em;
		background: white;
		padding: 1em;
		border-radius: 50%;
	}

	img {
		position: absolute;
		width: 4em;
		height: 4em;
	}
</style>
