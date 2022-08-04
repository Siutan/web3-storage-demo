<script context="module" lang="ts">
	export const prerender = true;
</script>

<script lang="ts">
	import Web3Retrieve from "../lib/web3/Web3Retrieve.svelte";
</script>

<svelte:head>
	<title>Retrieve</title>
	<meta name="description" content="Svelte Web3 Storage demo app" />
</svelte:head>

<section>
	<Web3Retrieve/>
</section>

<style>
	section {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		flex: 1;
	}

	h1 {
		width: 100%;
	}

</style>
