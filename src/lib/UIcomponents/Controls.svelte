<script lang="ts">
	import { createEventDispatcher } from 'svelte';

	const dispatch = createEventDispatcher();

	const icons = {
		play: '/play.png',
		pause: '/pause.png'
	};
</script>

<button on:click={() => dispatch('play')}>
	<img src={icons.play} alt="play" />
</button>
