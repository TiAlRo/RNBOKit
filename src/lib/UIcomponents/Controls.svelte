<script lang="ts">
	import { createEventDispatcher } from 'svelte';

	/** @type {string} */
	export let tag;

	const dispatch = createEventDispatcher();
</script>

<button on:click={() => dispatch('sendMessage')}>
	<img src="/{tag}.png" alt={tag} />
</button>
