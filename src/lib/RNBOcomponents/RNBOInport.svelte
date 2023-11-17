<script>
	import Controls from '../UIcomponents/Controls.svelte';
	import { TimeNow, MessageEvent } from '@rnbo/js';

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
