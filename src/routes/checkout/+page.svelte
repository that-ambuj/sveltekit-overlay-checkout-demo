<script>
	import OverlayCheckout from '../../+overlay.svelte';
</script>

<svelte:head>
	<title>Overlay Checkout Demo</title>
	<meta name="description" content="Experience our seamless overlay checkout process" />
</svelte:head>

<div class="min-h-screen bg-gray-50 py-8 dark:bg-gray-900">
	<div class="container mx-auto px-4">
		<div class="mb-8 text-center">
			<h1 class="mb-4 text-4xl font-bold text-gray-900 dark:text-white">Overlay Checkout Demo</h1>
			<p class="text-lg text-gray-600 dark:text-gray-300">
				Test the DodoPayments overlay checkout integration in SvelteKit
			</p>
		</div>

		<OverlayCheckout />
	</div>
</div>
