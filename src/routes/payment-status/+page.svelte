<script lang="ts">
	import { onMount } from 'svelte';
	import { page } from '$app/stores';

	let paymentStatus = 'loading';
	let paymentDetails: {
		sessionId?: string;
		paymentIntentId?: string;
		timestamp?: string;
	} = {};

	onMount(() => {
		// Get URL parameters to determine payment status
		const urlParams = new URLSearchParams(window.location.search);
		const status = urlParams.get('status') || 'success';
		const sessionId = urlParams.get('session_id');
		const paymentIntentId = urlParams.get('payment_intent_id');

		paymentStatus = status;
		paymentDetails = {
			sessionId: sessionId || undefined,
			paymentIntentId: paymentIntentId || undefined,
			timestamp: new Date().toLocaleString()
		};
	});
</script>

<svelte:head>
	<title>Payment Status - Overlay Checkout Demo</title>
	<meta name="description" content="Payment processing status" />
</svelte:head>

<div class="min-h-screen bg-gray-50 py-8 dark:bg-gray-900">
	<div class="container mx-auto max-w-2xl px-4">
		<div class="rounded-lg bg-white p-8 shadow-lg dark:bg-gray-800">
			{#if paymentStatus === 'loading'}
				<div class="text-center">
					<div
						class="mx-auto mb-4 h-16 w-16 animate-spin rounded-full border-4 border-blue-500 border-t-transparent"
					></div>
					<h1 class="mb-2 text-2xl font-bold text-gray-900 dark:text-white">Processing Payment</h1>
					<p class="text-gray-600 dark:text-gray-300">
						Please wait while we confirm your payment...
					</p>
				</div>
			{:else if paymentStatus === 'success'}
				<div class="text-center">
					<div
						class="mx-auto mb-4 flex h-16 w-16 items-center justify-center rounded-full bg-green-100"
					>
						<svg
							class="h-8 w-8 text-green-600"
							fill="none"
							stroke="currentColor"
							viewBox="0 0 24 24"
						>
							<path
								stroke-linecap="round"
								stroke-linejoin="round"
								stroke-width="2"
								d="M5 13l4 4L19 7"
							></path>
						</svg>
					</div>
					<h1 class="mb-2 text-2xl font-bold text-green-600">Payment Successful!</h1>
					<p class="mb-6 text-gray-600 dark:text-gray-300">
						Thank you for your purchase. Your payment has been processed successfully.
					</p>
				</div>
			{:else if paymentStatus === 'cancelled'}
				<div class="text-center">
					<div
						class="mx-auto mb-4 flex h-16 w-16 items-center justify-center rounded-full bg-yellow-100"
					>
						<svg
							class="h-8 w-8 text-yellow-600"
							fill="none"
							stroke="currentColor"
							viewBox="0 0 24 24"
						>
							<path
								stroke-linecap="round"
								stroke-linejoin="round"
								stroke-width="2"
								d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-2.5L13.732 4c-.77-.833-1.964-.833-2.732 0L3.732 16.5c-.77.833.192 2.5 1.732 2.5z"
							></path>
						</svg>
					</div>
					<h1 class="mb-2 text-2xl font-bold text-yellow-600">Payment Cancelled</h1>
					<p class="mb-6 text-gray-600 dark:text-gray-300">
						Your payment was cancelled. No charges have been made to your account.
					</p>
				</div>
			{:else}
				<div class="text-center">
					<div
						class="mx-auto mb-4 flex h-16 w-16 items-center justify-center rounded-full bg-red-100"
					>
						<svg class="h-8 w-8 text-red-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
							<path
								stroke-linecap="round"
								stroke-linejoin="round"
								stroke-width="2"
								d="M6 18L18 6M6 6l12 12"
							></path>
						</svg>
					</div>
					<h1 class="mb-2 text-2xl font-bold text-red-600">Payment Failed</h1>
					<p class="mb-6 text-gray-600 dark:text-gray-300">
						There was an issue processing your payment. Please try again or contact support.
					</p>
				</div>
			{/if}

			{#if paymentStatus !== 'loading'}
				<div class="border-t pt-6">
					<h3 class="mb-4 text-lg font-semibold text-gray-900 dark:text-white">Payment Details</h3>
					<div class="space-y-2 text-sm">
						<div class="flex justify-between">
							<span class="text-gray-600 dark:text-gray-300">Status:</span>
							<span class="font-medium capitalize">{paymentStatus}</span>
						</div>
						{#if paymentDetails.sessionId}
							<div class="flex justify-between">
								<span class="text-gray-600 dark:text-gray-300">Session ID:</span>
								<span class="font-mono text-xs">{paymentDetails.sessionId}</span>
							</div>
						{/if}
						{#if paymentDetails.paymentIntentId}
							<div class="flex justify-between">
								<span class="text-gray-600 dark:text-gray-300">Payment Intent:</span>
								<span class="font-mono text-xs">{paymentDetails.paymentIntentId}</span>
							</div>
						{/if}
						<div class="flex justify-between">
							<span class="text-gray-600 dark:text-gray-300">Timestamp:</span>
							<span class="text-xs">{paymentDetails.timestamp}</span>
						</div>
					</div>
				</div>

				<div class="mt-8 space-y-3">
					<a
						href="/checkout"
						class="block w-full rounded-lg bg-blue-600 px-6 py-3 text-center font-semibold text-white transition-colors duration-200 hover:bg-blue-700"
					>
						Try Another Purchase
					</a>
					<a
						href="/"
						class="block w-full rounded-lg bg-gray-200 px-6 py-3 text-center font-semibold text-gray-800 transition-colors duration-200 hover:bg-gray-300 dark:bg-gray-700 dark:text-white dark:hover:bg-gray-600"
					>
						Back to Home
					</a>
				</div>
			{/if}
		</div>
	</div>
</div>
