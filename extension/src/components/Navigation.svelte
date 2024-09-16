<script>
	import browser, { storage } from "webextension-polyfill";
	import { onMount, afterUpdate } from "svelte";
	import FaArrowLeft from "svelte-icons/fa/FaArrowLeft.svelte";
	import IoIosSettings from "svelte-icons/io/IoIosSettings.svelte";
	export let page;

	const pages = [
		{
			icon: "home.svg",
			name: "Dashboard",
			page: 1,
		},
		{
			icon: "plus.svg",
			name: "Add Wallet",
			page: 3,
		},
		{
			icon: "arrow.svg",
			name: "Send Transaction",
			page: 4,
			special: true,
		},
		{
			icon: "wallet.svg",
			name: "Manage Wallets",
			page: 8,
		},
		{
			icon: "settings.svg",
			name: "Settings",
			page: 2,
		},
	];
</script>

<div id="navbar">
	{#each pages as { icon, name, page: p, special }}
		<div
			class={`icon ${p == page && "icon-active"} ${special && "special-icon"}`}
			on:click={() => {
				page = p;
			}}
			on:keypress={() => {}}
		>
			<img src={`../assets/icons/${icon}`} alt={p} />
		</div>
	{/each}
</div>

<style>
	#navbar {
		background: #0d0e12;
		width: 100%;
		height: 56px;
		display: flex;
		align-items: center;
		justify-content: space-around;
		/* padding: 0 1rem; */
		position: fixed;
		bottom: 0;
		left: 0;
		z-index: 10;
	}

	#navbar .icon {
		color: #57595d;
		height: 100%;
		width: auto;
		cursor: pointer;
		display: flex;
		align-items: center;
		padding: 0 0.5rem;
        transition: 0.3s ease-out;
	}
    
	#navbar .icon:hover img {
        opacity: 1;
	}
    
	#navbar .icon-active img {
        opacity: 0.8 !important;
		mix-blend-mode: normal !important;
	}
    
	#navbar .special-icon {
        border-radius: 137.143px;
		border: 4.8px solid #181a20;
		background: linear-gradient(180deg, #332a68 0%, #41327e 100%);
        transform: translateY(-0.5rem);
        aspect-ratio: 1;
	}
    
	#navbar .icon img {
        transition: 0.3s ease-out;
        mix-blend-mode: luminosity;
        opacity: 0.5;
		height: 1.65rem;
		width: auto;
	}

    #navbar .special-icon img {
        mix-blend-mode: normal;
        height: 1.4rem;
        opacity: 0.65;
    }
    
</style>
