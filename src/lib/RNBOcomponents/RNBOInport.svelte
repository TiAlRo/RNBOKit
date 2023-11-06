<script>
	import Controls from '../UIcomponents/Controls.svelte';
	import { TimeNow, MessageEvent } from '@rnbo/js';

	/** @type {string} */
	export let tag;
	/** @type {import ('@rnbo/js').Device} */
	export let device;

	const play = () => device.scheduleEvent(new MessageEvent(TimeNow, tag, ''));
</script>

<Controls on:play={play} />
