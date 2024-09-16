<script>
	import browser from "webextension-polyfill";
	import { afterUpdate } from "svelte";
	import { sign_authentication_request } from "../../../qubic-wasm/pkg/qubic_wasm";
	import Prompt from "./Prompt.svelte";
	import { short } from "../js/utils";

	export let page;
	export let keystore;
	export let service_name;
	export let activeId;
	let close = true;
	let password = "";
	let activeIdIndex = keystore.wallets.findIndex((w) => w.id == activeId);

	afterUpdate(async () => {
		activeIdIndex = keystore.wallets.findIndex((w) => w.id == activeId);
		if (Boolean(password)) {
			try {
				if (activeIdIndex != -1) {
					let auth = sign_authentication_request(
						keystore,
						password,
						service_name,
						activeIdIndex
					);
					console.log(auth);
					await browser.storage.session.set({ requestFunction: null });
					await browser.storage.session.set({
						responseFunction: { type: "accepted", value: auth },
					});
					page = 1;
				}
			} catch (e) {
				console.log(e);
			}
		}
	});
</script>

{#if !close}
	<Prompt bind:password {keystore} bind:close />
{/if}
<div id="authentication">
	<img src="../assets/qubic.svg" alt="Qubic Logo" class="qubic-logo" />

	<div id="message">
		<div class="service">
			Service "{service_name}"
		</div>
		<span> wants to connect to your wallet </span>
	</div>
	<div class="grant-div">
		<ul class="permissions">
			<li>
				<img src="../assets/icons/check.svg" alt="" />
				Can do XYZ
			</li>
			<li>
				<img src="../assets/icons/check.svg" alt="" />
				Can do ABC
			</li>
		</ul>

		<div class="input-cont">
			{#if keystore.wallets[activeIdIndex]}
				<div
					id="indicator"
					style="background-color: #29d44e;border-color: #29d44e;"
				></div>
			{:else}
				<div
					id="indicator"
					style="background-color: #d43d29;border-color: #d43d29;"
				></div>
			{/if}
			<div class="input-field">
				{keystore.wallets[activeIdIndex]
					? `${keystore.wallets[activeIdIndex]?.name} | ${short(activeId)}`
					: "No Active Wallet"}
			</div>
		</div>

		<button
			id="sign"
			on:click={() => {
				close = false;
			}}
			>Sign Authentication

			<img src="../assets/icons/arrow-round.svg" alt="Connect" />
		</button>
	</div>
</div>

<style>
	#authentication {
		display: flex;
		flex-direction: column;
		justify-content: space-between;
		align-items: center;

		background-color: #1a1d26;
		flex: 1;
		padding: 1.5rem 1rem;
		width: 100%;
	}

	.grant-div {
		display: flex;
		flex-direction: column;
		height: max-content;
	}

	.qubic-logo {
		width: 116px;
		-webkit-user-drag: none;
		user-select: none;
		-webkit-user-select: none;
		margin-top: 1rem;
	}

	.input-cont {
		width: 100%;
		user-select: none;
		height: max-content;
		position: relative;
		display: flex;
		flex-direction: column;
		align-items: center;
		padding-bottom: 0;
		margin-bottom: 8px;
		margin-top: 1.5rem;
	}

	.input-field {
		padding: 0 1rem;
		border-radius: 16px;
		width: 100%;
		height: 48px;
		display: flex;
		align-items: center;
		margin: 0;
		padding-left: 3rem;
		color: rgba(255, 255, 255, 0.8);
		border: 2px solid #2e2e2e;
		background: #131313;
		font-size: 1rem;
	}

	.input-field:focus {
		outline: none;
	}

	.input-cont #indicator {
		width: 14px;
		height: 14px;
		border: solid 1px;
		border-radius: 20px;
		position: absolute;
		top: 50%;
		left: 1.5rem;
		transform: translateY(-50%);
	}

	.permissions {
		display: flex;
		flex-direction: column;
		gap: 0.5rem;
		padding: 1rem;
		border-radius: 28px;
		background: #121212;
		width: 100%;
		padding-bottom: 3rem;
	}

	.permissions li {
		display: flex;
		flex-direction: row;
		align-items: center;
		gap: 0.5rem;
		color: rgba(255, 255, 255, 0.7);
		text-align: center;
		font-size: 16px;
		line-height: 24px; /* 171.429% */
	}

	.permissions img {
		width: 24px;
		opacity: 0.8;
	}

	#sign {
		width: 320px;
		padding: 0.5rem 1rem;
		height: 3rem;
		font-size: 1rem;
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

	#sign:disabled {
		opacity: 0.5;
		/* pointer-events: none; */
		cursor: not-allowed;
	}

	#sign:active {
		background-color: #161616;
	}

	#sign img {
		mix-blend-mode: luminosity;
		opacity: 0.8;
		height: 1.5rem;
	}

	#sign:hover {
		border: 1px solid #6d6d6d;
	}

	#message {
		display: flex;
		flex-direction: column;
		gap: 0.25rem;
	}

	#message span {
		max-width: 280px;
		text-align: center;
		font-size: 18px;
		font-style: normal;
		font-weight: 400;
		opacity: 0.6;
		line-height: 24px;
	}

	#message .service {
		color: #fff;
		text-align: center;
		font-family: Inter;
		font-size: 20px;
		font-style: normal;
		font-weight: 600;
		line-height: 24px; /* 120% */
	}
</style>
