<script>
	import RadioGroup from '$lib/UIcomponents/RadioGroup.svelte';
	import RadioItem from '$lib/UIcomponents/RadioItem.svelte';
	/** @type {MIDIAccess|null|false} */
	let midi = $state(null); // global MIDIAccess object
	/**@type {MIDIInputMap|null} */
	let inports = $state(null); // available MIDI inports

	//TODO: choose port by name rather than index

	
	/** @type {MIDIInput|null} */
	let inport = null;
	
	/**
	 * @typedef {Object} Props
	 * @property {number|null} [port]
	 * @property {Uint8Array} midiMessage
	 */

	/** @type {Props} */
	let { port = $bindable(null), midiMessage = $bindable() } = $props();

	/**
	 * set the midi access point and get the midi input port
	 * @param {MIDIAccess} midiAccess
	 */
	function onMIDISuccess(midiAccess) {
		midi = midiAccess;
		inports = midi.inputs; // store in the global (in real usage, would probably keep in an object instance)
		if (port) {
			setPort();
		}
	}

	/**
	 * handle if external MIDI not available
	 * @param {string} msg
	 */
	function onMIDIFailure(msg) {
		console.error(`Failed to get MIDI access - ${msg}`);
		midi = false;
	}

	/**
	 * set the input port when selected
	 */
	function setPort() {
		if (inports === null || port === null) return;

		// @ts-ignore - ts getting their types wrong...
		inport = [...inports][port];
		console.log(
			// @ts-ignore - because inports will always exist, since we use the #if block
			'connecting to MIDI input port: %c' + inports.get(inport[0]).name,
			'font-style:italic'
		);
		// @ts-ignore - see previous comment
		inports.get(inport[0]).onmidimessage = onMIDIMessage;
	}

	/** @param {MIDIMessageEvent} event */
	function onMIDIMessage(event) {
		midiMessage = event.data;
		console.log(
			'MIDI message from %c' + event.target.name + ': ' + midiMessage,
			'font-style:italic'
		);
	}

	navigator.requestMIDIAccess().then(onMIDISuccess, onMIDIFailure);
</script>

<div class="RNBOsubcomponent">
	<p class="RNBOtext">External MIDI inputs</p>

	{#if midi && inports}
		<!-- <slot {midi} {inports} {port}> -->
		<RadioGroup>
			{#each [...inports.entries()] as input, i}
				<!-- first index of the input array is the id, second the object -->
				<RadioItem bind:group={port} name="inports" value={i} on:change={setPort}
					>{input[1].name}
				</RadioItem>
			{/each}
		</RadioGroup>
		<!-- </slot> -->
	{:else if midi === false}
		<p>MIDI access failed, please try in a different browser</p>
	{:else}
		<p>loading MIDI ports</p>
	{/if}
</div>
