<script>
	import FaRandom from "svelte-icons/fa/FaRandom.svelte";
	import FaCopy from "svelte-icons/fa/FaCopy.svelte";
	import Prompt from "./Prompt.svelte";
	import {
		add_wallet,
		get_id,
		get_random_seed,
		get_entities,
	} from "../../../qubic-wasm/pkg/qubic_wasm";
	import { afterUpdate } from "svelte";
	import { setValue } from "../js/settings";

	let password = "";
	let close = true;
	export let keystore;
	export let page;
	export let entities;
	export let settings;
	let seed = "";
	let name = `Wallet-${keystore.wallets.length + 1}`;

	let placeholder = "Seed must be 55 lowercase characters";
	let valid = false;

	afterUpdate(async () => {
		if (seed.length == 55) {
			try {
				placeholder = get_id(seed);
				valid = true;
			} catch {}
		} else {
			placeholder = "* Seed must be 55 lowercase characters";
			valid = false;
		}

		if (Boolean(password) && seed.length == 55) {
			keystore = add_wallet(keystore, password, name, seed);
			setValue("keystore", keystore);

			try {
				entities = (await get_entities(keystore, settings.rpc)) ?? [];
				keystore.wallets.forEach((w, idx) => {
					entities[idx].name = w.name;
				});
			} catch (e) {
				console.log(e);
				entities = keystore.wallets.map((w) => {
					return {
						id: w.id,
						name: w.name,
						incoming_amount: 0,
						outgoing_amount: 0,
					};
				});
			}

			page = 1;
		}
	});

	function randomize() {
		seed = get_random_seed();
	}

	function copy() {
		if (valid) {
			navigator.clipboard.writeText(seed);
		}
	}

	function short(id) {
		return `${id.slice(0, 4)}...${id.slice(56, 60)}`;
	}
</script>

{#if !close}
	<Prompt bind:password {keystore} bind:close />
{/if}
<div id="add-wallet">
	<div class="separator no-pad"></div>
	<div class="container">
		<input
			class="id-output"
			autocomplete="off"
			placeholder="Wallet Name"
			bind:value={name}
		/>
		<input
			class="id-output"
			autocomplete="off"
			placeholder="Wallet Seed"
			bind:value={seed}
		/>
		<input class="id-output" autocomplete="off" placeholder="Public ID" />
		<h5>{valid ? short(placeholder) : placeholder}</h5>

		<div style="display: none; width: 250px; justify-content: space-evenly;">
			<button class="randomizer" on:click={randomize}>
				<FaRandom />
			</button>

			<button class="randomizer" on:click={copy}>
				<FaCopy />
			</button>
		</div>
		<!-- 
        <input
        disabled
		id="id-output"
		style="text-align: center;"
		placeholder={valid ? short(placeholder) : ""}
        /> -->

		<button
			type="submit"
			id="unlock-button"
			on:click={() => {
				close = false;
			}}>
            <img src="../assets/icons/plus.svg" alt="Add Wallet">
            Add Wallet</button
		>
		<p>
			Don't risk losing your funds. Protect your wallet by saving your Seed in a
			place you trust. It's the only way to recover your wallet if you get
			locked out of the app, update it or get a new device.
		</p>
	</div>
</div>

<style>
	#add-wallet {
		background-color: #1a1d26;
		flex: 1;
		padding: 0 1rem;
		width: 100%;
	}

	.container {
		display: flex;
		flex-direction: column;
		width: 100%;
		padding-top: 1rem;
	}

	.container h5 {
		font-size: 14px;
		color: #fff;
		opacity: 0.6;
		line-height: 24px;
		margin-bottom: 1rem;
        font-weight: 400;
	}
	.container p {
		font-size: 14px;
		color: #fff;
        line-height: 150%;
		opacity: 0.4;
        font-weight: 300;
        margin-top: 0.5rem;
        text-align: center;
	}

	.id-output {
		border-radius: 16px;
		border: 1px solid #313131;
		background: #21242e;
		width: 320px;
		padding: 0.5rem 1rem;
		height: 3rem;
		color: rgba(253, 253, 253, 0.856);
		font-family: Inter;
		font-size: 1rem;
		font-style: normal;
		font-weight: 500;
		line-height: 24px;
	}

	.id-output:focus {
		outline: none;
		border: 1px solid #6d6d6d;
	}

	#unlock-button {
		width: 320px;
		padding: 0.5rem 1rem;
		height: 3rem;
		font-size: 1rem;
		border-radius: 10px;
		border-radius: 16px;
		color: rgba(255, 255, 255, 0.8);
		border: 1px solid #2e2e2e;
		background: #0b0b0b;
		cursor: pointer;

        display: flex;
        align-items: center;
        justify-content: center;
        gap: 0.5rem;
	}

    #unlock-button img {
        mix-blend-mode: luminosity;
        opacity: 0.8;
        height: 1.5rem;
    }

	#unlock-button:hover {
		border: 1px solid #6d6d6d;
	}

	.randomizer {
		display: flex;
		justify-content: center;
		align-items: center;
		background-color: #27a8db;
		border: none;
		border-radius: 10px;
		color: whitesmoke;
		height: 2.5em;
	}
</style>