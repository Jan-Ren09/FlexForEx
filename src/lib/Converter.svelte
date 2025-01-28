<script lang="ts">
	import { convertCurrency, CURRENCIES } from './api';

	let fromCurrency = $state('USD');
	let toCurrency = $state('PHP');
	let amount = $state(1);
	let result = $state<number | null>(null);
	let error = $state<string | null>(null);
	let loading = $state(false);
	let currentDate = $state<string | null>(null);

	$effect(() => {
		if (amount !== null) {
			result = null;
			currentDate = null;
		}
	});

	interface ConversionResult {
		result: number;
		timestamp: number;
		date: string;
	}

	async function handleConvert() {
		if (!amount) return;

		loading = true;
		error = null;
		result = null;
		currentDate = null;

		try {
			const conversionResult = (await convertCurrency(
				fromCurrency,
				toCurrency,
				amount
			)) as ConversionResult;
			result = conversionResult.result;
			currentDate = conversionResult.date;
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

<div class="flex min-h-screen items-center justify-center bg-gray-400 p-4">
	<div
		class="w-full max-w-lg rounded-2xl border border-black bg-gray-950 p-6 text-center shadow-xl"
	>
		<!-- App Title -->
		<h1 class="mb-8 text-3xl font-bold text-[#FFD700]">FlexForEx</h1>

		<!-- Converter Card -->
		<div class="w-full rounded-xl bg-[#333333] p-6 shadow-2xl">
			<div class="space-y-4">
				<!-- Amount and From Currency -->
				<div class="flex flex-col space-y-4 md:flex-row md:space-x-4 md:space-y-0">
					<!-- Amount Input -->
					<div class="flex-1">
						<label for="amount" class="mb-1 block text-sm font-medium text-[#F5F5F5]">Amount</label>
						<input
							id="amount"
							type="number"
							bind:value={amount}
							class="w-full rounded-md border border-gray-300 px-3 py-2 text-center focus:ring-2 focus:ring-blue-500"
						/>
					</div>

					<!-- From Currency Dropdown -->
					<div class="flex-1">
						<label for="fromCurrency" class="mb-1 block text-sm font-medium text-[#F5F5F5]"
							>From Currency</label
						>
						<select
							id="fromCurrency"
							bind:value={fromCurrency}
							class="w-full rounded-md border border-gray-300 px-3 py-2 text-center focus:ring-2 focus:ring-blue-500"
						>
							{#each CURRENCIES as currency}
								<option value={currency}>{currency}</option>
							{/each}
						</select>
					</div>
				</div>

				<!-- To Currency Dropdown -->
				<div class="flex-1">
					<label for="toCurrency" class="mb-1 block text-sm font-medium text-[#F5F5F5]"
						>To Currency</label
					>
					<select
						id="toCurrency"
						bind:value={toCurrency}
						class="w-full rounded-md border border-gray-300 px-3 py-2 text-center focus:ring-2 focus:ring-blue-500"
					>
						{#each CURRENCIES as currency}
							<option value={currency}>{currency}</option>
						{/each}
					</select>
				</div>

				<!-- Convert Button -->
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

				<!-- Result Display -->
				{#if result !== null}
					<div class="mt-4 text-center text-sm text-[#F5F5F5]">
						As of {currentDate}
					</div>
					<div class="mt-2 rounded-lg border bg-white p-4 text-center">
						<h1 class="text-xl font-semibold text-gray-800">
							{amount}
							{fromCurrency} = {result.toFixed(2)}
							{toCurrency}
						</h1>
					</div>
				{/if}

				<!-- Error Display -->
				{#if error}
					<div class="mt-4 text-center text-red-600">
						{error}
					</div>
				{/if}
			</div>
		</div>
	</div>
</div>
