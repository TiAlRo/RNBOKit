<script>
	import Controls from '../UIcomponents/Controls.svelte';
	import { TimeNow, MessageEvent } from '@rnbo/js';

	/** @type {import ('@rnbo/js').MessageInfo} */
	export let inport;
	/** @type {import ('@rnbo/js').Device} */
	export let device;

	const play = () => device.scheduleEvent(new MessageEvent(TimeNow, inport.tag, ''));
</script>

<Controls on:play={play} />
