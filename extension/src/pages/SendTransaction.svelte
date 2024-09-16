<script>
	import browser from "webextension-polyfill";
	import { send_transaction } from "../../../qubic-wasm/pkg/qubic_wasm";
	import { short } from "../js/utils";
	import Prompt from "./Prompt.svelte";
	import { afterUpdate } from "svelte";
    import FaPlus from "svelte-icons/fa/FaPlus.svelte";

	export let keystore;
	export let settings;
	export let address_index;
	export let current_tick;
	export let page;

	console.log(keystore, address_index);

	let password = "";
	let close = true;
	let timeout = false;

	let to = "";
	let amount;
	let tick_offset = "20";

	afterUpdate(async () => {
		if (Boolean(password) && !timeout) {
			timeout = true;
			try {
				await send_transaction(
					keystore,
					password,
					address_index,
					settings.rpc,
					to,
					BigInt(amount),
					current_tick + Number(tick_offset),
					0,
					0
				);
				password = "";
				close = true;

				await browser.notifications.create("", {
					type: "basic",
					title: "QSOL Wallet Notification",
					iconUrl: browser.runtime.getURL("assets/qbc1024.png"),
					message: `Transaction ${short(keystore.wallets[address_index].id)} -> ${short(to)} | Amount: ${BigInt(amount)} QUs\nwas submitted successfully for tick ${current_tick + Number(tick_offset)}`,
				});
				page = 1;
			} catch (e) {
				password = "";
				close = true;

				await browser.notifications.create("", {
					type: "basic",
					title: "QSOL Wallet Notification",
					iconUrl: browser.runtime.getURL("assets/qbc1024.png"),
					message: `RPC error while submitting transaction ${e}`,
				});
				page = 1;
			}
		}
	});
</script>

{#if !close}
	<Prompt bind:password {keystore} bind:close />
{/if}
<div id="send-transaction">
    <div class="separator no-pad"></div>
	{#if keystore.wallets.length == 0}
		<div class="nowallet">
			<h4>No wallets found...</h4>
			<h5>It seems like you don't have a wallet yet.</h5>

			<button
				id="add-button"
				on:click={() => {
					page = 3;
				}}
			>
				<div style="aspect-ratio: 1; height: 18px;">
					<FaPlus />
				</div>
				Create New Wallet
			</button>
		</div>
	{:else}
		<div class="container">
			<div class="input-section">
				<label for="from">From:</label>
				<input
					name="from"
					class="input-field"
					placeholder={`${keystore.wallets[address_index].name} | ${short(keystore.wallets[address_index].id)}`}
					disabled
					/>
			</div>
			<div class="input-section">
				<label for="to">To:</label>
				<input
					list="known-addresses"
					class="input-field"
					type="text"
					name="to"
					bind:value={to}
				/>
				<datalist
					id="known-addresses"
					on:select={(e) => {
						to = e.target.value;
					}}
				>
					{#each keystore.wallets as wallet, idx}
						{#if idx !== address_index}
							<option value={wallet.id}>{wallet.name}</option>
						{/if}
					{/each}
				</datalist>
			</div>
			<div class="input-section">
				<label for="amount">Amount:</label>
				<input class="input-field" name="amount" type="number" bind:value={amount} />
			</div>
			<div class="input-section">
				<label for="tick">Tick Offset:</label>
				<select class="input-field" name="tick" bind:value={tick_offset}>
					<option value="10">10</option>
					<option selected value="20">20</option>
					<option value="30">30</option>
					<option value="40">40</option>
					<option value="50">50</option>
				</select>
			</div>

			<button
				id="submit-button"
				on:click={() => {
					close = false;
				}}
			>
				Submit Transaction
				<img src="../assets/icons/arrow.svg" alt="Send Transaction">
			</button>
		</div>
	{/if}
</div>

<style>
	#send-transaction {
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

	.input-section {
        display: flex;
        flex-direction: column;
        gap: 0.125rem;
    }

    .input-section label {
        font-size: 14px;
        color: #fff;
        opacity: 0.6;
        line-height: 24px;
        margin-left: 0.25rem;
        font-weight: 400;
    }

	.input-field {
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

	.input-field:focus {
		outline: none;
		border: 1px solid #6d6d6d;
	}
	#submit-button {
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
		margin-top: 0.5rem;

        display: flex;
        align-items: center;
        justify-content: center;
        gap: 0.5rem;
	}

	#submit-button:active {
		background-color: #161616;
	}

    #submit-button img {
        mix-blend-mode: luminosity;
        opacity: 0.8;
        height: 1.25rem;
    }

	#submit-button:hover {
		border: 1px solid #6d6d6d;
	}

	.nowallet {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		width: 100%;
		height: max-content;
		padding: 1.5rem 1.5rem;
        margin-top: 1rem;
		border-radius: 17px;
		background-color: #22242d;
		box-shadow: rgba(0, 0, 0, 0.2) 0px 7px 29px 0px;
	}

	.nowallet h4 {
		color: #fff8;
		font-size: 1.25rem;
		font-weight: 600;
		margin: 0;
	}

	.nowallet h5 {
		color: #fff5;
		font-size: 14px;
		font-weight: 500;
		margin: 0;
		margin-top: 0.5rem;
		text-align: center;
		margin-bottom: 1rem;
	}

    .nowallet #add-button {
		width: 250px;
		height: 40px;
		background-color: #44454d;
		border: none;
		border-radius: 10px;
		color: whitesmoke;
		display: flex;
		align-items: center;
		justify-content: center;
		gap: 6px;
		cursor: pointer;
		font-size: 1rem;
		font-weight: 400;
		margin-top: 1rem;
	}
</style>
