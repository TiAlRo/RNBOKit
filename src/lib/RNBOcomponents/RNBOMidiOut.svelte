<script>
	import { MIDIEvent, TimeNow } from '@rnbo/js';
	// import ExtMidiOut from './ExtMidiOut.svelte';
	
	
	
	/**
	 * @typedef {Object} Props
	 * @property {number} [port] - type {number} - the MIDI port index
	 * @property {import ('@rnbo/js').Device} device
	 * @property {import('@rnbo/js').MIDIData|undefined} [midiMessage]
	 * @property {import('svelte').Snippet<[any]>} [children]
	 */

	/** @type {Props & { [key: string]: any }} */
	let {
		port = 0,
		device,
		midiMessage = new MIDIEvent(TimeNow, 0, [144, 100, 100]),
		children,
		...rest
	} = $props();
	// /** @type {number|undefined} */
	// export let midiChannel = 0;

	//TODO: replace block below with a midiMessage receive from a Max Midi out, send to extMidiOut
	device.midiEvent.subscribe((ev) => {
		console.log(ev);
	});
</script>

<div class="RNBOcomponent RNBOsection" {...rest}>
	{#if children}{@render children({ port, })}{:else}
		<div class="RNBOtag">in {port}</div>

		<!-- <ExtMidiOut bind:midiMessage /> -->
	{/if}
</div>
