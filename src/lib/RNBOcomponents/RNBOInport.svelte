<script>
	import Controls from '../UIcomponents/Controls.svelte';
	import rnbo from '@rnbo/js';
	const { TimeNow, MessageEvent } = rnbo;

	/** @type {string} */
	export let tag;
	/** @type {import ('@rnbo/js').Device} */
	export let device;

	const sendMessage = () => {
		const messageEvent = new MessageEvent(TimeNow, tag);
		device.scheduleEvent(messageEvent);
	};
</script>

<Controls {tag} on:sendMessage={sendMessage} />
