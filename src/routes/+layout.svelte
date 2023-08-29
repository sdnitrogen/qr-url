<script lang="ts">
	import { Toast } from '@skeletonlabs/skeleton';
	// The ordering of these imports is critical to your app working properly
	import '@skeletonlabs/skeleton/themes/theme-skeleton.css';
	// If you have source.organizeImports set to true in VSCode, then it will auto change this ordering
	import '@skeletonlabs/skeleton/styles/skeleton.css';
	// Most of your app wide CSS should be put in this file
	import '../app.postcss';

	import { LightSwitch } from '@skeletonlabs/skeleton';
</script>

<Toast />
<header class="fixed top-0 z-40 w-full">
	<div class="flex flex-1 h-16 px-8 items-center space-x-4 justify-end">
		<LightSwitch />
	</div>
</header>
<slot />
