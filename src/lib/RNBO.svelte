<script>
	import './RNBO.css';
	import { onMount } from 'svelte';
	import rnbo from '@rnbo/js';
	const { BaseDevice, createDevice } = rnbo;
	import RNBOParam from './RNBOcomponents/RNBOParam.svelte';
	import RNBOOutport from './RNBOcomponents/RNBOOutport.svelte';
	import RNBOInport from './RNBOcomponents/RNBOInport.svelte';
	import RNBOInlet from './RNBOcomponents/RNBOInlet.svelte';
	import RNBOMidiIn from './RNBOcomponents/RNBOMidiIn.svelte';
	// import RNBOMidiOut from './RNBOcomponents/RNBOMidiOut.svelte';
	import path from 'path-browserify';

	// Create AudioContext
	/** @type {AudioContext|undefined} */
	let context = $state(undefined);

	const loadContext = () => {
		// @ts-ignore
		let WAContext = window.AudioContext || window.webkitAudioContext;
		context = new WAContext({ sampleRate: 44100 });
	};

	// Create device

	/** @type {import ('@rnbo/js').IPatcher|undefined} */
	let patcher = $state(undefined);

	/** @type {import ('@rnbo/js').ExternalDataInfo[]|undefined} */
	let dependencyFileCorrected = $state(undefined);

	/** @typedef {object} signalPort
	 * @property {number} index - the port index
	 * @property {string} tag - the tag
	 * @property {'signal'} type - the type
	 * @property {string} meta - the meta
	 */

	/**
	 * @typedef {Object} Props
	 * @property {string} [patchName]
	 * @property {import ('@rnbo/js').Device|undefined} [device]
	 * @property {import ('@rnbo/js').Parameter[]} [parameters]
	 * @property {import ('@rnbo/js').MessageInfo[]} [inports]
	 * @property {signalPort[]} [inlets]
	 * @property {Array<Number>} [midiOutports]
	 * @property {import ('@rnbo/js').MessageInfo[]} [outports]
	 * @property {signalPort[]} [outlets]
	 * @property {Array<Number>} [midiInports]
	 * @property {import('svelte').Snippet<[any]>} [children]
	 */

	/** @type {Props} */
	let {
		patchName,
		device = $bindable(undefined),
		parameters = $bindable([]),
		inports = $bindable([]),
		inlets = $bindable([]),
		midiOutports = $bindable([]),
		outports = $bindable([]),
		outlets = $bindable([]),
		midiInports = $bindable([]),
		children
	} = $props();

	// set up device
	const deviceSetup = async () => {
		const modules = import.meta.glob('/src/RNBO/export/*'),
			exportPath = '/src/RNBO/export/';

		//import the patcher json dynamically!
		const patchPath = path.join(exportPath, patchName);
		patcher = await modules[patchPath]();

		if (patcher && context) {
			//import the dependency json dynamically!
			const dependenciesPath = path.join(exportPath, 'dependencies.json');
			const dependencyFile = (await modules[dependenciesPath]()).default;

			dependencyFileCorrected = dependencyFile.map((dependency) => {
				if (BaseDevice.bufferDescriptionHasRemoteURL(dependency)) {
					return dependency;
				}
				const newFile = path.join(exportPath, dependency.file);
				return Object.assign({}, dependency, { file: newFile });
			});

			device = await createDevice({ context, patcher });

			//load dependencies if they exist
			if (dependencyFileCorrected && dependencyFileCorrected.length) {
				device.loadDataBufferDependencies(dependencyFileCorrected);
			}
			device.node.connect(context.destination);

			parameters = device.parameters;

			inports = device.inports;

			// @ts-expect-error - desc.inlets does indeed exist
			inlets = patcher.desc.inlets.filter((inlet) => inlet.type === 'signal');

			midiInports = Array.from({ length: device.numMIDIInputPorts }, (_, i) => i + 1);

			outports = device.outports;

			// @ts-expect-error - desc.outlets does indeed exist
			outlets = patcher.desc.outlets.filter((outlet) => outlet.type === 'signal');

			midiOutports = Array.from({ length: device.numMIDIOutputPorts }, (_, i) => i + 1);
		} else if (!patcher) {
			throw new Error('No patcher found!');
		}
		if (!context) {
			throw new Error('No context found!');
		}
	};

	// load on client side only (no Window server side)
	onMount(async () => {
		loadContext();
		deviceSetup();
	});
</script>

<!-- html -->
{#if patcher && device && context}
	<div
		onclick={() => context?.resume()}
		onkeydown={() => context?.resume()}
		role="button"
		tabindex="-2"
	>
		{#if children}{@render children({
				patcher,
				device,
				dependencyFileCorrected,
				parameters,
				context,
				inports,
				inlets,
				outports,
				outlets,
				midiInports,
				midiOutports
			})}{:else}
			<div class="RNBOsection">
				<!-- use the json file name as header -->
				<h1>patch.export.json</h1>

				<!-- create input for each MIDI input port -->
				{#if midiInports.length > 0}
					<div class="RNBOsection">
						<h2>MIDI inputs</h2>
						{#each midiInports as port}
							<RNBOMidiIn {port} {device} />
						{/each}
					</div>
				{/if}

				<!-- handle all signal inlets -->
				{#if inlets.length > 0}
					<div class="RNBOsection">
						<h2>signal inlets</h2>
						{#each inlets as inlet}
							<RNBOInlet {inlet} {device} />
						{/each}
					</div>
				{/if}

				<!-- handle all event inlets -->
				{#if inports.length > 0}
					<div class="RNBOsection">
						<!-- list only the event inlets (skip signal ones) -->
						<h2>message inlets</h2>
						{#each inports as inport}
							<RNBOInport tag={inport.tag} {device} />
						{/each}
					</div>
				{/if}

				<!-- handle all parameters -->
				{#if parameters.length > 0}
					<div class="RNBOsection">
						<h2>parameters</h2>
						{#each parameters as parameter}
							<RNBOParam {parameter} />
						{/each}
					</div>
				{/if}

				<!-- handle all event outlets -->
				{#if outports.length > 0}
					<div class="RNBOsection">
						<h2>message outlets</h2>
						{#each outports as outport}
							<RNBOOutport {outport} {device} />
						{/each}
					</div>
				{/if}

				<!-- midi outs not working yet (RNBO bug?????), exclude for now -->
				<!-- <div class="RNBOsection">
					<h2>MIDI outputs</h2>
					{#each Array(device.numMIDIOutputPorts) as _, port}
						<RNBOMidiOut {port} {device} />
					{/each}
				</div> -->
			</div>
		{/if}
	</div>
{:else}
	<!-- show a placeholder skeleton when loading -->
	<div class="RNBOsection">
		<div class="RNBOplaceholder"></div>
		<div class="RNBOsection">
			<div class="RNBOplaceholder"></div>
			<div class="RNBOsection">
				<div class="RNBOplaceholder"></div>
			</div>
		</div>
		<div class="RNBOsection">
			<div class="RNBOplaceholder"></div>
		</div>
		<div class="RNBOsection">
			<div class="RNBOplaceholder"></div>
		</div>
	</div>
{/if}
