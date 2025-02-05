<script>
	import Controls from '../UIcomponents/Controls.svelte';
	import rnbo from '@rnbo/js';
	const { TimeNow, MessageEvent } = rnbo;

	/**
	 * @typedef {Object} Props
	 * @property {string} tag
	 * @property {import ('@rnbo/js').Device} device
	 */

	/** @type {Props} */
	let { tag, device } = $props();

	const sendMessage = () => {
		const messageEvent = new MessageEvent(TimeNow, tag);
		device.scheduleEvent(messageEvent);
	};
</script>

<Controls {tag} {sendMessage} />
