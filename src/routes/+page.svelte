<script>
	import CurrencyConverter from '$lib/Converter.svelte';

	if (!import.meta.env.VITE_RAPIDAPI_KEY) {
		console.warn('VITE_RAPIDAPI_KEY is not set');
	} else {
		console.log('API key loaded properly!');
	}
</script>

<CurrencyConverter />
