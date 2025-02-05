<script>
	import ProgressBar from '$lib/UIcomponents/ProgressBar.svelte';

	/** @type {import ('@rnbo/js').MessagePayload } */
	let value = $state(0);

	/**
	 * @typedef {Object} Props
	 * @property {import ('@rnbo/js').MessageInfo} outport
	 * @property {import ('@rnbo/js').Device} device
	 * @property {number} [min]
	 * @property {number} [max]
	 */

	/** @type {Props & { [key: string]: any }} */
	let { outport, device, min = 0, max = 1, ...rest } = $props();

	$effect(() => {
		device.messageEvent.subscribe((ev) => {
			if (ev.tag == outport.tag) {
				value = ev.payload;
			}
		});
	});
</script>

<div class="RNBOcomponent RNBOsection" {...rest}>
	<div class="RNBOalign">
		<div class="RNBOtag">{outport.tag}</div>
		<!-- some hack to make the value max 2 decimal floats: -->
		<div class="RNBOval">{+parseFloat(value).toFixed(2)}</div>
	</div>

	<p class="RNBOtext">{outport.meta.slice(1, -1)}</p>
	<ProgressBar value={Array.isArray(value) ? value[0] : value} {max} {min} />
</div>
