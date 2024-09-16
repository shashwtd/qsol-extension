<script>
	import FaPlus from "svelte-icons/fa/FaPlus.svelte";
	import FaTrash from "svelte-icons/fa/FaTrash.svelte";
	import FaLocationArrow from "svelte-icons/fa/FaLocationArrow.svelte";
	import FaRegCopy from "svelte-icons/fa/FaRegCopy.svelte";
	import { afterUpdate, onMount } from "svelte";
	import { setValue } from "../js/settings";
	import browser from "webextension-polyfill";
	// import { title } from 'svelte-icons/components/IconBase.svelte';

	export let settings;
	export let page;
	export let entities;
	export let keystore;
	export let address_index;
	export let activeId;
	export let url;

	let wallets = entities.map((e) => {
		return {
			id: e.public_key,
			name: e.name,
			balance: e.incoming_amount - e.outgoing_amount,
		};
	});

	afterUpdate(() => {
		console.log(activeId);
		wallets = entities.map((e) => {
			return {
				id: e.public_key,
				name: e.name,
				balance: e.incoming_amount - e.outgoing_amount,
			};
		});
		totalBalance = wallets
			.map((w) => w.balance)
			.reduce((part, a) => part + a, 0);
	});

	let totalBalance = wallets
		.map((w) => w.balance)
		.reduce((part, a) => part + a, 0);

	function getIdent(num, fmt) {
		switch (fmt) {
			case "1e0":
				return "";
			case "1e3":
				return "k";
			case "1e6":
				return "Mn";
			case "1e9":
				return "Bn";
			case "auto":
				return getIdent(num, String(fmt_auto(num)));
		}
	}

	function fmt_number(num) {
		switch (settings.num_fmt) {
			case "auto":
				return num / Number(fmt_auto(num));
			case "1e0":
				return num;
			case "1e3":
				return num / 1e3;
			case "1e6":
				return num / 1e6;
			case "1e9":
				return num / 1e9;
		}
	}

	function fmt_auto(num) {
		switch (true) {
			case num < 2600:
				return "1e0";
			case num < 26e5:
				return "1e3";
			case num < 26e8:
				return "1e6";
			default:
				return "1e9";
		}
	}

	function short(id) {
		return `${id.slice(0, 6)} . . . ${id.slice(56, 60)}`;
	}

	function delete_wallet(idx) {
		console.log("test", idx);
		entities.splice(idx, 1);
		entities = entities;
		keystore.wallets.splice(idx, 1);

		setValue("keystore", keystore);
	}
</script>

<div id="manageWallets">
	<div class="separator no-pad"></div>
	<div id="wallets">
		{#if wallets.length > 0}
			{#each wallets as wallet, idx}
		<div class="wallet">
			<div class="wallet-info">
				<div class="wallet-name">
					<img src="../assets/icons/ext-link.svg" alt="" />
					{wallet.name}
				</div>
				<div
					class="action-icon del-icon"
					on:keypress={() => {}}
					on:click={() => delete_wallet(idx)}
				>
					<FaTrash />
				</div>
			</div>
			<div class="wallet-actions">
				<div class="wallet-balance">
					{fmt_number(wallet.balance).toLocaleString(undefined, {
								maximumFractionDigits: 3,
							})}
					QUs
				</div>
				<div class="wallet-more-info">
					<div
						on:keypress={() => {}}
						on:click={() => {
							browser.tabs.create({
								url: `https://app.qubic.li/network/explorer/address/${wallet.id}`,
							});
						}}
						class="wallet-id"
					>
						{short(wallet.id)}
						<!-- ksjhfs . . . weqa -->
					</div>
					<div class="actions">
						<div
							class="action-icon"
							on:keypress={() => {}}
							on:click={() => {
								navigator.clipboard.writeText(wallet.id);
							}}
						>
							<FaRegCopy />
						</div>
					</div>
				</div>
			</div>
		</div>
		{/each}
		{:else}
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
		{/if}
	</div>
</div>

<style>
	#manageWallets {
		height: max-content;
		min-height: 434px;
		background-color: #1a1d26;
		padding: 0 1rem;
		padding-bottom: 4rem;
	}

	#wallets {
		display: flex;
		flex-direction: column;
		padding-bottom: 1.25rem;
		padding-top: 1rem;
	}

	/* WALLLET */

	.wallet {
		display: flex;
		flex-direction: column;
		align-items: start;
		border-radius: 17px;
		background-color: #292b35;
		margin-bottom: 1rem;
		width: 100%;
		height: max-content;
		padding: 0.25rem 1rem;
	}

	.wallet .wallet-info {
		border-bottom: 1px solid #ffffff28;
		flex-direction: row !important;
		justify-content: space-between !important;
		align-items: center !important;

		display: flex;
		width: 100%;
		padding: 0.5rem 0;
		align-items: center;
	}

	.wallet .wallet-actions {
		display: flex;
		flex-direction: column;
		width: 100%;
		padding: 0.5rem 0;
	}

	.wallet .wallet-actions .wallet-more-info {
		display: flex;
		flex-direction: row;
		justify-content: space-between;
		width: 100%;
		align-items: center;
	}

	.wallet .wallet-name {
		color: #999;
		font-variant-numeric: lining-nums proportional-nums;
		font-family: "Space Grotesk";
		font-size: 1rem;
		font-style: normal;
		font-weight: 500;
		line-height: 1.5rem;

		display: flex;
		align-items: center;
		cursor: pointer;
		gap: 0.5rem;
	}
	.wallet .wallet-name:hover {
		color: #6f88ce;
	}

	.wallet .wallet-name img {
		width: 1rem;
		height: 1rem;
	}

	.wallet .wallet-balance {
		font-variant-numeric: lining-nums proportional-nums;
		font-family: Inter;
		font-size: 20px;
		font-style: normal;
		font-weight: 700;
		line-height: 24px; /* 120% */

		background: linear-gradient(180deg, #6cb3c3 0%, #b591c6 100%);
		background-clip: text;
		-webkit-background-clip: text;
		-webkit-text-fill-color: transparent;
	}

	.wallet .wallet-id {
		overflow: hidden;
		color: #d9d9d9;
		font-variant-numeric: lining-nums proportional-nums;
		text-overflow: ellipsis;
		font-family: "Space Grotesk";
		font-size: 16px;
		font-style: normal;
		font-weight: 400;
		line-height: 24px; /* 171.429% */
		display: flex;
		gap: 0.5rem;
		align-items: center;
		transition-duration: 0.2s;
	}

	.wallet .wallet-actions .actions {
		display: flex;
		align-items: center;
		gap: 0.5rem;
	}

	.wallet .action-icon {
		cursor: pointer;
		height: 1.75rem;
		padding: 0.4rem;
		border-radius: 4px;
		background-color: #44454d;
	}

	.wallet .action-icon:hover {
		background-color: #55565f;
	}

	.nowallet {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		width: 100%;
		height: max-content;
		padding: 1.5rem 1.5rem;
		border-radius: 17px;
		background-color: #22242d;
		box-shadow: rgba(0, 0, 0, 0.2) 0px 7px 29px 0px;
		margin-bottom: 1rem;
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

	#add-button {
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

	.activity-indicator {
		width: 1rem;
		height: 1rem;
		border: gray solid 1px;
		border-radius: 50%;
	}

	.del-icon {
		color: #CE6262;
	}

	/*#overview h3 {
        color: #27a8db;
    }*/
</style>
