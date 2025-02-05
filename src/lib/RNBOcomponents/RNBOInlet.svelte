<script>
	import RadioGroup from '$lib/UIcomponents/RadioGroup.svelte';
	import RadioItem from '$lib/UIcomponents/RadioItem.svelte';
	import AudioDropIn from './AudioDropIn.svelte';
	import MicIn from './MicIn.svelte';

	let context = device.context;

	/** @typedef {object} inlet
	 * @property {number} index - the port index
	 * @property {string} tag - the tag
	 * @property {'signal'} type - the type
	 * @property {string} meta - the meta
	 */

	/** @type {AudioNode} */
	let audio = $state();

	/** @type {{default: 'default', mic: 'mic', dropIn: 'dropIn'}} */
	let modes = { default: 'default', mic: 'mic', dropIn: 'dropIn' };

	/**
	 * @typedef {Object} Props
	 * @property {import ('@rnbo/js').Device} device
	 * @property {inlet} [inlet]
	 * @property {'default'|'mic'|'dropIn'} [mode]
	 * @property {import('svelte').Snippet<[any]>} [children]
	 */

	/** @type {Props & { [key: string]: any }} */
	let {
		device,
		inlet = { index: 1, tag: 'in1', type: 'signal', meta: 'something' },
		mode = $bindable(modes.default),
		children,
		...rest
	} = $props();
	//set a defaultMode so the radio group only shows if no mode has been specified
	/** @type {'default'|'mic'|'dropIn'} */
	let defaultMode = mode;
	//make sure the default mode is mic in the radio group
	if (mode == modes.default) mode = modes.mic;

	$effect(() => {
		if (audio) {
			audio.connect(device.node, inlet.index - 1);
		}
	});
</script>

<div class="RNBOcomponent RNBOsection" {...rest}>
	{#if children}{@render children({ context, audio })}{:else}
		<div class="RNBOtag">{inlet.tag}</div>
		{#if defaultMode === modes.default}
			<RadioGroup>
				<RadioItem bind:group={mode} name="Mic" value={modes.mic}>Mic</RadioItem>
				<RadioItem bind:group={mode} name="DropIn" value={modes.dropIn}>Drop-in</RadioItem>
			</RadioGroup>
		{/if}
		{#if mode === modes.dropIn}
			<AudioDropIn class="RNBOsubcomponent" bind:audio {context} />
		{:else}
			<MicIn bind:audio {context} />
		{/if}
	{/if}
</div>
