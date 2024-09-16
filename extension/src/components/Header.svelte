<script>
	import browser from "webextension-polyfill";
	import { afterUpdate } from "svelte";
	import FaArrowLeft from "svelte-icons/fa/FaArrowLeft.svelte";
	import IoIosSettings from "svelte-icons/io/IoIosSettings.svelte";
	export let page;
	let pageName = getPageName();

	afterUpdate(() => {
		pageName = getPageName();
	});

	function getPageName() {
		switch (page) {
			case 1:
				return "Dashboard";
			case 2:
				return "Settings";
			case 3:
				return "Add Wallet";
			case 4:
				return "Send Transaction";
			case 6:
				return "Request Authentication";
			case 7:
				return "Transaction Request";
			case 8:
				return "Your Wallets";
			default:
				return "";
		}
	}
</script>

{#if getPageName !== ""}
	<div id="header">
		<h1>
			{pageName}
		</h1>
	</div>
{/if}

<style>
	#header {
		width: 100%;
		height: max-content;
		background: #1a1d26;
		padding: 0 1rem;
	}

	#header h1 {
		font-size: 1.25rem;
		line-height: 1.75rem;
		font-weight: 600;
		opacity: 90%;
		margin-top: 1rem;
		margin-bottom: 0.75rem;
		margin-left: 0.25rem;
	}
</style>
