<script>
	import RangeSlider from '$lib/UIcomponents/RangeSlider.svelte';
	import RadioGroup from '$lib/UIcomponents/RadioGroup.svelte';
	import RadioItem from '$lib/UIcomponents/RadioItem.svelte';

	/**
	 * @typedef {Object} Props
	 * @property {import ('@rnbo/js').Parameter} parameter
	 */

	/** @type {Props & { [key: string]: any }} */
	let { parameter = $bindable(), ...rest } = $props();
</script>

<div class="RNBOcomponent RNBOsection" {...rest}>
	{#if parameter.enumValues}
		<div class="RNBOtag">{parameter.name}</div>
		<RadioGroup>
			{#each parameter.enumValues as enumValue, i}
				<RadioItem bind:group={parameter.value} name="justify" value={i}>{enumValue}</RadioItem>
			{/each}
		</RadioGroup>
	{:else}
		<div class="RNBOalign">
			<div class="RNBOtag">{parameter.name}</div>
			<!-- some hack to make the value max 2 decimal floats: -->
			<div class="RNBOval">{+parseFloat(value).toFixed(2)} / {parameter.max}</div>
		</div>
		<RangeSlider
			name="range-slider"
			bind:value={parameter.value}
			min={parameter.min}
			max={parameter.max}
			step={0.01}
		/>
	{/if}
</div>
