<script>
	import browser from "webextension-polyfill";
	export let keystore;
	export let url;
	export let page;

	export let enabled = 0;

	function short(id) {
		return `${id.slice(0, 4)}...${id.slice(56, 60)}`;
	}

	async function connect() {
		let ids = keystore.wallets.map((w) => {
			return w.id;
		});
		ids.splice(enabled, 1);
		ids.unshift(keystore.wallets[enabled].id);
		await browser.storage.session.set({
			responseFunction: { type: "accepted", value: ids },
		});
		await browser.storage.session.set({ requestFunction: null });
		let conn =
			(await browser.storage.local.get("connectedApp")).connectedApp ?? [];

		conn.push({ url: url, id: keystore.wallets[enabled].id });
		await browser.storage.local.set({ connectedApp: conn });

		page = 1;
	}
</script>

<div id="request-accounts">
	<img src="../assets/qubic.svg" alt="Qubic Logo" class="qubic-logo" />
	<div id="message">
		<div class="url">
			{url}
		</div>
		<span> wants to connect to your wallet </span>
	</div>
	<div class="grant-div">
		<ul class="permissions">
			<li>
				<img src="../assets/icons/check.svg" alt="" />
				View your Wallet's Balance
			</li>
			<li>
				<img src="../assets/icons/check.svg" alt="" />
				Request a transaction
			</li>
		</ul>

		<div class="wallet-cont">
			{#if enabled == 0}
				<div
					id="indicator"
					style="background-color: #d43d29;border-color: #d43d29;"
				></div>
			{:else}
				<div
					id="indicator"
					style="background-color: #29d44e;border-color: #29d44e;"
				></div>
			{/if}

			<select
				name="wallet"
				id="wallet-select"
				class="wallet-select"
				onchange={(e) => {
					enabled = e.target.value;
				}}
			>
				{#if keystore.wallets.length == 0}
					<option value={0}>No Wallets Found</option>
				{:else}
					{#each keystore.wallets as wallet, idx}
						<option value={idx}>
							{wallet.name} [{short(wallet.id)}]
						</option>
					{/each}
				{/if}
			</select>
		</div>
		<button
			id="connect"
			disabled={enabled == 0}
			on:click={enabled != 0 ? connect : null}
			>Connect Wallet
			<img src="../assets/icons/link.svg" alt="Connect" /></button
		>
	</div>
</div>

<style>
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
	#request-accounts {
		display: flex;
		flex-direction: column;
		justify-content: space-between;
		align-items: center;

		background-color: #1a1d26;
		flex: 1;
		padding: 1.5rem 1rem;
		width: 100%;
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

	#message .url {
		color: #fff;
		text-align: center;
		font-family: Inter;
		font-size: 20px;
		font-style: normal;
		font-weight: 600;
		line-height: 24px; /* 120% */
	}

	.wallet-cont {
		width: 100%;
		height: max-content;
		position: relative;
		display: flex;
		flex-direction: column;
		align-items: center;
		padding-bottom: 0;
		margin-bottom: 8px;
		margin-top: 1.5rem;
	}

	.wallet-select {
		padding: 0 1rem;
		border-radius: 16px;
		width: 100%;
		height: 48px;
		margin: 0;
		padding-left: 3rem;
		color: rgba(255, 255, 255, 0.8);
		border: 2px solid #2e2e2e;
		background: #131313;
		cursor: pointer;
		font-size: 1rem;
	}

	.wallet-select:focus {
		outline: none;
	}

	.wallet-cont #indicator {
		width: 14px;
		height: 14px;
		border: solid 1px;
		border-radius: 20px;
		position: absolute;
		top: 50%;
		left: 1.5rem;
		transform: translateY(-50%);
	}

	#connect {
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

	#connect:disabled {
		opacity: 0.5;
		/* pointer-events: none; */
		cursor: not-allowed;
	}

	#connect:active {
		background-color: #161616;
	}

	#connect img {
		mix-blend-mode: luminosity;
		opacity: 0.8;
		height: 1.5rem;
	}

	#connect:hover {
		border: 1px solid #6d6d6d;
	}
</style>
