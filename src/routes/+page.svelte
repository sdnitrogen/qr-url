<script lang="ts">
	import { Clipboard, Download } from 'lucide-svelte';
	import QRCode, { type QRCodeToDataURLOptions } from 'qrcode';
	import { clipboard, toastStore, type ToastSettings } from '@skeletonlabs/skeleton';

	const t: ToastSettings = {
		message: 'ERROR! Try again.',
		background: 'variant-filled-error'
	};

	let qrSrc = '/linkr_sdnitrogen.png';
	let inputUrl = '';
	let shortURL = 'linkr.bio/sdnitrogen';
	let clipURL = 'https://linkr.bio/sdnitrogen';
	let copied = false;
	let opts = {
		errorCorrectionLevel: 'H',
		type: 'image/png',
		margin: 2.5,
		width: 256,
		color: {
			dark: '#000000FF',
			light: '#FFFFFFFF'
		}
	};

	const handleCopy = () => {
		copied = true;
		setTimeout(() => {
			copied = false;
		}, 1000);
	};

	const handleSubmit = async (event: Event) => {
		const formEl = event.target as HTMLFormElement;
		const data = new FormData(formEl);
		inputUrl = (data.get('inputUrl') as string) ?? '';

		inputUrl &&
			QRCode.toDataURL(inputUrl, opts as QRCodeToDataURLOptions, (err, code) => {
				if (err) {
					console.log(err);
					toastStore.trigger(t);
				}
				qrSrc = code;
			});

		await fetch(`https://api.shrtco.de/v2/shorten?url=${inputUrl}`)
			.then((response) => response.json())
			.then((data) => {
				shortURL = data.result.short_link;
				clipURL = data.result.full_short_link;
			})
			.catch((err) => {
				console.log(err);
				toastStore.trigger(t);
			});
	};
</script>

<div class="container h-full mx-auto flex justify-center items-center gap-12">
	<div class="flex flex-col items-start justify-start space-y-5 w-[32rem] h-[30rem]">
		<h2 class="h2">Better URL</h2>
		<p>
			Enter the URL of your site to get a custom QR code and <code class="code text-sm"
				>a cute short url</code
			> in seconds!
		</p>
		<form class="w-full" on:submit|preventDefault={handleSubmit}>
			<input
				name="inputUrl"
				class="input variant-form-material"
				type="url"
				placeholder="Enter URL here"
				required
			/>
			<button type="submit" class="btn variant-filled my-4">Generate</button>
		</form>
	</div>
	<span class="divider-vertical h-[35rem] m-0" />
	<div
		class="flex flex-col items-center justify-start card variant-ghost-error w-80 h-[30rem] shadow-xl p-8 gap-8"
	>
		<div class="flex items-center justify-center card bg-white dark:bg-white w-64 h-64 shadow-xl">
			<img class="h-auto max-w-full rounded-lg" src={qrSrc} alt="" />
		</div>
		<div class="input-group variant-form-material w-64 input-group-divider grid-cols-[1fr_auto]">
			<input
				class="variant-form-material"
				type="text"
				value={copied ? 'Copied!' : shortURL}
				disabled
			/>
			<button
				class="btn-icon bg-transparent !px-[0.5rem]"
				use:clipboard={clipURL}
				on:click={handleCopy}><Clipboard /></button
			>
		</div>
		<a download="output-qr-code.png" href={qrSrc}>
			<button type="button" class="btn variant-filled rounded-sm w-64">
				<Download />
				<span>Download</span>
			</button>
		</a>
	</div>
</div>
