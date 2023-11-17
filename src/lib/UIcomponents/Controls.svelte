<script lang="ts">
	import { createEventDispatcher } from 'svelte';

	export let tag: string;

	const dispatch = createEventDispatcher();
</script>

<button on:click={() => dispatch('sendMessage')}>
	<img src="/{tag}.png" alt={tag} />
</button>
