<script>
	/**
	 * @typedef {Object} Props
	 * @property {any} name
	 * @property {number} [value]
	 * @property {number} [min]
	 * @property {number} [max]
	 * @property {number} [step]
	 * @property {string} [label]
	 */

	/** @type {Props} */
	let {
		name,
		value = $bindable(0),
		min = 0,
		max = 100,
		step = 0.01,
		label = '',
		onclick,
		onchange,
		onblur
	} = $props();
</script>

<input
	type="range"
	{name}
	class="RNBOslider"
	aria-label={label}
	{min}
	{max}
	{step}
	bind:value
	{onclick}
	{onchange}
	{onblur}
/>
