<script>
	/**
	 * @typedef {Object} Props
	 * @property {any} [value]
	 * @property {number} [min]
	 * @property {number} [max]
	 * @property {string} [labelledby]
	 */

	/** @type {Props} */
	let {
		value = void 0,
		min = 0,
		max = 100,
		labelledby = ''
	} = $props();
	let fillPercent = $derived(value ? (100 * (value - min)) / (max - min) : 0);
</script>

<!-- Track -->
<div
	class="RNBOprogress-bar"
	data-testid="progress-bar"
	role="progressbar"
	aria-labelledby={labelledby}
	aria-valuenow={value}
	aria-valuemin={min}
	aria-valuemax={max - min}
>
	<!-- Meter -->
	<div class="RNBOprogress-bar-meter" style:width="{fillPercent}%"></div>
</div>

<style>
	.RNBOprogress-bar {
		overflow: hidden;
		width: 100%;
		height: 6px;
		border-radius: var(--local-rounded-base);
		border-style: solid;
		border-color: rgba(var(--local-surface-color), 0.5);
		border-width: 0.5px;
		background-color: white;
	}
	.RNBOprogress-bar-meter {
		height: 100%;
		border-radius: var(--local-rounded-base);
		background-color: rgb(var(--local-accent-color));
	}
</style>
