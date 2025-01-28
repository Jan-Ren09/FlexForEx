<script lang="ts">
	import { convertCurrency, CURRENCIES } from './api';

	let fromCurrency = $state('USD');
	let toCurrency = $state('PHP');
	let amount = $state(1);
	let result = $state<number | null>(null);
	let error = $state<string | null>(null);
	let loading = $state(false);
	interface ConversionResult {
		result: number;
		timestamp: number;
		date: string;
	}

	async function handleConvert() {
		if (!amount) return;

		loading = true;
		error = null;
		try {
			const conversionResult = (await convertCurrency(
				fromCurrency,
				toCurrency,
				amount
			)) as ConversionResult;
			result = conversionResult.result;
		} catch (err: unknown) {
			if (err instanceof Error) {
				error = 'Conversion failed: ' + err.message;
			} else {
				error = 'Conversion failed. Please try again.';
			}
		} finally {
			loading = false;
		}
	}
</script>

<div
	class="flex min-h-screen items-center justify-center bg-gradient-to-br from-blue-100 to-purple-200 p-4"
>
	<h1 class="flex items-center justify-center text-3xl font-bold text-gray-800">FlexForEx</h1>
	<div class="w-full max-w-md rounded-xl bg-white p-8 shadow-2xl">
		<div class="space-y-4">
			<div class="flex space-x-4">
				<div class="flex-1">
					<label for="amount" class="mb-1 block text-sm font-medium text-gray-700">Amount</label>
					<input
						id="amount"
						type="number"
						bind:value={amount}
						class="w-full rounded-md border border-gray-300 px-3 py-2 focus:ring-2 focus:ring-blue-500"
					/>
				</div>

				<div class="flex-1">
					<label for="fromCurrency" class="mb-1 block text-sm font-medium text-gray-700"
						>From Currency</label
					>
					<select
						id="fromCurrency"
						bind:value={fromCurrency}
						class="w-full rounded-md border border-gray-300 px-3 py-2 focus:ring-2 focus:ring-blue-500"
					>
						{#each CURRENCIES as currency}
							<option value={currency}>{currency}</option>
						{/each}
					</select>
				</div>
			</div>

			<div class="flex-1">
				<label for="toCurrency" class="mb-1 block text-sm font-medium text-gray-700"
					>To Currency</label
				>
				<select
					id="toCurrency"
					bind:value={toCurrency}
					class="w-full rounded-md border border-gray-300 px-3 py-2 focus:ring-2 focus:ring-blue-500"
				>
					{#each CURRENCIES as currency}
						<option value={currency}>{currency}</option>
					{/each}
				</select>
			</div>

			<button
				onclick={handleConvert}
				disabled={loading}
				class="w-full rounded-md bg-blue-600 py-3 text-white transition-colors hover:bg-blue-700 {loading
					? 'cursor-not-allowed opacity-50'
					: ''}"
			>
				{#if loading}
					Converting...
				{:else}
					Convert
				{/if}
			</button>

			{#if result !== null}
				<div class="mt-4 text-center">
					<p class="text-xl font-semibold text-gray-800">
						{amount}
						{fromCurrency} = {result.toFixed(2)}
						{toCurrency}
					</p>
				</div>
			{/if}

			{#if error}
				<div class="mt-4 text-center text-red-600">
					{error}
				</div>
			{/if}
		</div>
	</div>
</div>
