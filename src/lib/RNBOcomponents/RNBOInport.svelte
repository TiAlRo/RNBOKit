<script>
	import Controls from '../UIcomponents/Controls.svelte';
	import { TimeNow, MessageEvent } from '@rnbo/js';

	/** @type {import ('@rnbo/js').MessageInfo} */
	export let inport;
	/** @type {import ('@rnbo/js').Device} */
	export let device;
	/** @type {number[]} */
	export let value = [];

	$: device.scheduleEvent(new MessageEvent(TimeNow, inport.tag, value));
</script>

<Controls />
