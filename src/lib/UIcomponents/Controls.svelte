<script lang="ts">
	interface Props {
		tag: string;
		sendMessage: () => void;
	}

	let { tag, sendMessage }: Props = $props();
</script>

<button onclick={sendMessage}>
	<img src="/{tag}.png" alt={tag} />
</button>
