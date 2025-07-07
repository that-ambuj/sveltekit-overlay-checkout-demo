<script lang="ts">
	import { onMount } from 'svelte';
	import type { CheckoutEvent } from 'dodopayments-checkout';

	// Mock items list - you can move this to a separate constants file if needed
	const ITEMS_LIST = [
		{
			title: 'Premium Product',
			altText: 'Premium product image',
			imageUrl: '/favicon.svg' // Using the existing favicon as placeholder
		}
	];

	interface CheckoutState {
		status: 'idle' | 'loading' | 'open' | 'error';
		error?: string;
	}

	let checkoutState: CheckoutState = { status: 'idle' };
	let message = '';

	const handleCheckoutEvents = (event: CheckoutEvent) => {
		console.log('Checkout event:', event);

		switch (event.event_type) {
			case 'checkout.opened':
				checkoutState = { status: 'open' };
				message = 'Checkout opened';
				break;
			case 'checkout.closed':
				checkoutState = { status: 'idle' };
				message = 'Checkout closed';
				break;
			case 'checkout.redirect':
				checkoutState = { status: 'loading' };
				window.location.href = event.data?.url;
				message = 'Redirecting to payment page';
				break;
			case 'checkout.error':
				checkoutState = {
					status: 'error',
					error: event.data?.message || 'An error occurred'
				};
				message = 'An error occurred';
				break;
		}
	};

	const handleCheckout = () => {
		try {
			checkoutState = { status: 'loading' };

			// Dynamic import to handle potential SSR issues
			import('dodopayments-checkout').then(({ DodoPayments }) => {
				DodoPayments.Checkout.open({
					redirectUrl: `${window.location.origin}/payment-status`,
					products: [
						{
							productId: 'pdt_QWovnGwqARBqUQQbmlEI7',
							quantity: 1
						}
					]
				});
			});
		} catch (error) {
			checkoutState = {
				status: 'error',
				error: error instanceof Error ? error.message : 'Failed to open checkout'
			};
		}
	};

	onMount(async () => {
		// Dynamic import to avoid SSR issues
		const { DodoPayments } = await import('dodopayments-checkout');

		DodoPayments.Initialize({
			displayType: 'overlay',
			linkType: 'static',
			mode: 'test',
			theme: 'dark',
			onEvent: handleCheckoutEvents
		});
	});
</script>

<div class="mx-auto max-w-4xl p-6">
	<div class="mb-8 text-center">
		<h2 class="mb-4 text-3xl font-bold text-gray-800 dark:text-white">
			Experience Our Overlay Checkout
		</h2>
	</div>

	<div class="overflow-hidden rounded-lg bg-white shadow-lg dark:bg-gray-800">
		<div class="md:flex">
			<!-- Product Image -->
			<div class="md:w-1/2">
				<div class="flex h-64 items-center justify-center bg-gray-200 md:h-full dark:bg-gray-700">
					<img src={ITEMS_LIST[0].imageUrl} alt={ITEMS_LIST[0].altText} class="max-h-32 w-auto" />
				</div>
			</div>

			<!-- Product Details -->
			<div class="p-6 md:w-1/2">
				<h3 class="mb-4 text-xl font-semibold text-gray-800 dark:text-white">
					{ITEMS_LIST[0].title}
				</h3>

				<p class="mb-6 text-gray-600 dark:text-gray-300">
					Click the button below and experience our seamless overlay checkout process. Fast, secure,
					and designed for the best checkout experience.
				</p>

				<div class="space-y-4">
					<button
						on:click={handleCheckout}
						disabled={checkoutState.status === 'loading' || checkoutState.status === 'open'}
						class="w-full rounded-lg bg-blue-600 px-6 py-3 font-semibold text-white transition-colors duration-200 hover:bg-blue-700 disabled:bg-gray-400"
					>
						{#if checkoutState.status === 'loading'}
							Processing...
						{:else if checkoutState.status === 'open'}
							Checkout Open
						{:else}
							Buy Now
						{/if}
					</button>

					<button
						class="w-full rounded-lg bg-gray-200 px-6 py-3 font-semibold text-gray-800 transition-colors duration-200 hover:bg-gray-300 dark:bg-gray-700 dark:text-white dark:hover:bg-gray-600"
					>
						Learn More
					</button>
				</div>

				{#if message}
					<div
						class="mt-4 rounded-lg border border-blue-200 bg-blue-50 p-3 dark:border-blue-700 dark:bg-blue-900"
					>
						<p class="text-sm text-blue-800 dark:text-blue-200">
							{message}
						</p>
					</div>
				{/if}

				{#if checkoutState.status === 'error' && checkoutState.error}
					<div
						class="mt-4 rounded-lg border border-red-200 bg-red-50 p-3 dark:border-red-700 dark:bg-red-900"
					>
						<p class="text-sm text-red-800 dark:text-red-200">
							Error: {checkoutState.error}
						</p>
					</div>
				{/if}
			</div>
		</div>
	</div>
</div>

