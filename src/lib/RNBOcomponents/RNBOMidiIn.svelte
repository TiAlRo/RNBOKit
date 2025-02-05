<script>
	import rnbo from '@rnbo/js';
	const { MIDIEvent, TimeNow } = rnbo;
	import XyMidiIn from './XYMidiIn.svelte';
	import ExtMidiIn from './ExtMidiIn.svelte';
	import RadioGroup from '$lib/UIcomponents/RadioGroup.svelte';
	import RadioItem from '$lib/UIcomponents/RadioItem.svelte';

	/** @type {{default: 'default', xy: 'xy', external: 'external'}} */
	let modes = { default: 'default', xy: 'xy', external: 'external' };

	/**
	 * @typedef {Object} Props
	 * @property {number} [port] - type {number} - the MIDI port index
	 * @property {import ('@rnbo/js').Device} device
	 * @property {import('@rnbo/js').MIDIData|undefined} [midiMessage]
	 * @property {number|undefined} [midiChannel]
	 * @property {'default'|'xy'|'external'} [mode]
	 * @property {import('svelte').Snippet<[any]>} [children]
	 */

	/** @type {Props & { [key: string]: any }} */
	let {
		port = 0,
		device,
		midiMessage = $bindable(undefined),
		midiChannel = 0,
		mode = $bindable(modes.default),
		children,
		...rest
	} = $props();

	//set a defaultMode so the radio group only shows if no mode has been specified
	/** @type {'default'|'xy'|'external'} */
	let defaultMode = mode;
	//make sure the default mode is mic in the radio group
	if (mode == modes.default) mode = modes.xy;

	$effect(() => {
		if (midiMessage && midiMessage.length > 0) {
			console.log('from MidiIn', midiMessage);
			device.scheduleEvent(new MIDIEvent(TimeNow, port, midiMessage));
		}
	});
</script>

<div class="RNBOcomponent RNBOsection" {...rest}>
	{#if children}{@render children({ midiChannel })}{:else}
		<div class="RNBOtag">in {port}</div>
		{#if defaultMode === modes.default}
			<RadioGroup>
				<RadioItem bind:group={mode} name="xy" value={modes.xy}>XY-pad</RadioItem>
				<RadioItem bind:group={mode} name="external" value={modes.external}>ext. MIDI</RadioItem>
			</RadioGroup>
		{/if}

		{#if mode === modes.external}
			<ExtMidiIn bind:midiMessage {midiChannel} />
		{:else}
			<XyMidiIn bind:midiMessage {midiChannel} />
		{/if}
	{/if}
</div>
