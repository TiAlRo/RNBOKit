<script>
	let {
		files = $bindable(void 0),
		name,
		lead,
		message,
		meta,
		ondragenter,
		ondragleave,
		onchange,
		ondragover,
		ondrop,
		onclick,
		onkeydown,
		onkeyup,
		onkeypress,
		...rest
	} = $props();
	let dragOver = $state(false);
</script>

<div class="RNBOdropzone" data-testid="file-dropzone">
	<!-- Input: File (hidden) -->
	<!-- NOTE: keep `bind:files` here, unlike FileButton -->
	<input
		bind:files
		type="file"
		{name}
		{...rest}
		class="RNBOdropzone-input"
		class:RNBOdrag={dragOver}
		ondragenter={() => {
			dragOver = true;
			ondragenter();
		}}
		ondragleave={() => {
			dragOver = false;
			ondragleave();
		}}
		{onchange}
		{ondragover}
		{ondrop}
		{onclick}
		{onkeydown}
		{onkeyup}
		{onkeypress}
	/>
	<!-- Interface -->
	<div class="RNBOdropzone-interface">
		<div class="dropzone-interface-text">
			<!-- Lead -->
			{#if lead}<div class="RNBOdropzone-lead">{@render lead?.()}</div>{/if}
			<!-- Message -->
			<div class="RNBOdropzone-message">
				{#if message}{@render message()}{:else}<strong>Upload files</strong> or drag and drop{/if}
			</div>
			<!-- Meta Text -->
			{#if meta}<small class="RNBOdropzone-meta">{@render meta?.()}</small>{/if}
		</div>
	</div>
</div>

<style>
	.RNBOdropzone {
		display: flex;
		position: relative;
		padding: 1rem;
		padding-top: 2rem;
		padding-bottom: 2rem;
		justify-content: center;
		align-items: center;
		border-width: 2px;
		border-style: dashed;
		border-radius: var(--local-rounded-container);
		border-color: rgb(var(--local-surface-color));
		transition: var(--local-animation) var(--local-animation-duration);
		transition-timing-function: var(--local-animation-function);
	}
	.RNBOdrag {
		background-color: rgba(var(--local-accent-color), 0.2);
		transition: var(--local-animation) var(--local-animation-duration);
		transition-timing-function: var(--local-animation-function);
	}
	.RNBOdropzone:hover {
		background-color: rgba(var(--local-accent-color), 0.2);
		transition: var(--local-animation) var(--local-animation-duration);
		transition-timing-function: var(--local-animation-function);
	}
	.RNBOdropzone:active {
		background-color: rgba(var(--local-accent-color), 0.5);
		transition: var(--local-animation) var(--local-animation-duration);
		transition-timing-function: var(--local-animation-function);
	}
	.RNBOdropzone-input {
		position: absolute;
		top: 0;
		right: 0;
		bottom: 0;
		left: 0;
		width: 100%;
		cursor: pointer;
		opacity: 0;
	}
	.RNBOdropzone-interface {
		display: flex;
		text-align: center;
		justify-content: center;
		align-items: center;
	}
	/* .RNBOdropzone-interface-text { */
	/* } */
	.RNBOdropzone-meta {
		opacity: 0.75;
	}
	.RNBOdropzone-lead {
		margin-bottom: 1rem;
	}
</style>
