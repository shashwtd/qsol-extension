<script>
	import browser from "webextension-polyfill";
	import { send_transaction } from "../../../qubic-wasm/pkg/qubic_wasm";
	import { short } from "../js/utils";
	import Prompt from "./Prompt.svelte";
	import { afterUpdate } from "svelte";

	export let keystore;
	export let settings;
	export let current_tick;
	export let activeId;
	export let page;
	export let url;
	export let transaction_request = {
		to: "XOHYYIZLBNOAWDRWRMSGFTOBSEPATZLQYNTRBPHFXDAIOYQTGTNFTDABLLFA",
		amount: 10,
		input_size: 0,
		input_type: 0,
	};

	let password = "";
	let timeout = false;
	let close = true;

	let amount;
	let tick_offset = "20";

	afterUpdate(async () => {
		let address_index = keystore.wallets.findIndex((w) => w.id == activeId);
		if (Boolean(password) && !timeout) {
			timeout = true;
			try {
				await send_transaction(
					keystore,
					password,
					address_index,
					settings.rpc,
					transaction_request.to,
					BigInt(transaction_request.amount),
					current_tick + Number(tick_offset),
					transaction_request.input_type,
					transaction_request.input_size
				);
				password = "";

				close = true;
				await browser.storage.session.set({ requestFunction: null });
				await browser.storage.session.set({
					responseFunction: {
						type: "accepted",
						value: {
							from: activeId,
							to: transaction_request.to,
							amount: transaction_request.amount,
							tick: current_tick + Number(tick_offset),
							input_type: transaction_request.input_type,
							input_size: transaction_request.input_size,
						},
					},
				});

				browser.notifications.create("", {
					type: "basic",
					title: "QSOL Wallet Notification",
					iconUrl: browser.runtime.getURL("assets/qbc1024.png"),
					message: `Requested ${url} transaction ${activeId} -> ${transaction_request.to} | Amount: ${transaction_request.amount} QUs\nwas submitted successfully for tick ${current_tick + Number(tick_offset)}`,
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
	<div class="form-cont">
		<div class="input-box">
			<label for="requested-by">Request By:</label>
			<input id="requested-by" class="input-field" disabled placeholder={url} />
		</div>
		<div class="input-box">
			<label for="from">From:</label>
			<input
				id="from"
				class="input-field"
				disabled
				placeholder={short(activeId)}
			/>
		</div>
		<div class="input-box">
			<label for="to">To:</label>
			<input
				id="to"
				class="input-field id-input"
				type="text"
				disabled
				placeholder={short(transaction_request.to)}
			/>
		</div>
		<div class="input-box">
			<label for="amount">Amount:</label>
			<input
				id="amount"
				class="input-field"
				type="number"
				disabled
				placeholder={transaction_request.amount}
			/>
		</div>
		<div class="input-box">
			<label for="input-type">Input Type:</label>
			<input
				id="input-type"
				class="input-field"
				type="number"
				disabled
				placeholder={transaction_request.input_type}
			/>
		</div>
		<div class="input-box">
			<label for="input-size">Input Size:</label>
			<input
				id="input-size"
				class="input-field"
				type="number"
				disabled
				placeholder={transaction_request.input_size}
			/>
		</div>
		<div class="input-box">
			<label for="tick-offset">Tick Offset:</label>
			<select id="tick-offset" class="input-field" bind:value={tick_offset}>
				<option value="10">10</option>
				<option value="20">20</option>
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
        
        Sign Transaction
        <img src="../assets/icons/link.svg" alt="Sign Transaction" />
            
		</button>
	</div>
</div>

<style>
	#send-transaction {
		display: flex;
		align-items: center;
		flex-direction: column;
		background-color: #1a1d26;
		gap: 0.5rem;
		padding: 0 1rem;
		height: 100%;
		width: 100%;
	}

	.form-cont {
		padding-top: 1rem;
		display: flex;
		flex-direction: column;
		justify-content: space-between;
		align-items: center;
		gap: 0.5rem;
		width: 100%;
		border-top: #fff1 solid 2px;
	}

	.input-box {
		display: flex;
		align-items: center;
        justify-content: center;
		width: 100%;
		border-radius: 1rem;
        height: 3rem;

		color: rgba(255, 255, 255, 0.8);
		border: 1px solid #2e2e2e;
		background: #23252e;
		padding:0.25rem 1rem;
        padding-right: 0.25rem;
        gap: 0.5rem;
	}

	.input-box label {
		font-size: 0.875rem;
		width: max-content;
        text-wrap: nowrap;
	}

	.input-field {
		border: none;
		background: #171a1f;
		color: rgba(255, 255, 255, 0.8);
		padding: 0 1rem;
		font-size: 1rem;
        border-radius: 0.75rem;
        width: 100%;
        height: 100%;
        margin: 0;
	}

    .input-field:focus {
        outline: none;
        border: 1px solid #646972;
    }

	.id-input {
		font-size: small;
	}

	#submit-button {
		width: 320px;
		padding: 0.5rem 1rem;
		height: 3rem;
		font-size: 1rem;
		border-radius: 16px;
		color: rgba(255, 255, 255, 0.8);
		border: 1px solid #2e2e2e;
		background: #101010;
        margin-top: 0.5rem;
		cursor: pointer;

		display: flex;
		align-items: center;
		justify-content: center;
		gap: 0.5rem;
	}

	#submit-button:disabled {
		opacity: 0.5;
		/* pointer-events: none; */
		cursor: not-allowed;
	}

	#submit-button:active {
		background-color: #161616;
	}

	#submit-button img {
		mix-blend-mode: luminosity;
		opacity: 0.8;
		height: 1.5rem;
	}

	#submit-button:hover {
		border: 1px solid #6d6d6d;
	}
</style>
