<script lang="ts">
	import { createEventDispatcher } from "svelte";

	const dispatch = createEventDispatcher();

	const icons = {
		play: "/play.png",
		pause: "/pause.png",
	};

	export let isPlaying: Boolean;
</script>

<button on:click={() => dispatch("playPause")}>
	<img src={isPlaying ? icons.pause : icons.play} alt="play" />
</button>
