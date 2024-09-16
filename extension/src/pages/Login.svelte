<script>
    import browser from "webextension-polyfill";
	import { check_password, set_password } from "../../../qubic-wasm/pkg/qubic_wasm";
    import { setValue } from "../js/settings";

    export let page;
	export let keystore;

	let password = ""

	async function loginHandler() {
		if (Boolean(keystore.pwd)) {
			if (check_password(keystore, password)) {
				page = 1;
				await browser.storage.session.set({login: { logged_in: true }});
			} else {
				password = ""
				await browser.notifications.create("", {
					type: "basic",
					title: "QSOL Wallet Notification",
					iconUrl: browser.runtime.getURL("assets/qbc1024.png"),
					message: "Invalid password provided!"
				});
			}
		} else {
			keystore = set_password(password);
			console.log(keystore);
			await browser.storage.session.set({login: { logged_in: true }});
			setValue("keystore", keystore);
			page = 1;
		}
	}
</script>

<div id="login">
	<img src="../assets/qubic.svg" alt="Qubic Logo"/>
	<div class="title-group">

		<div style="font-size: 2rem; font-weight:700;">Qubic Wallet</div>
		<h2>
			Create a Password to Continue
		</h2>
	</div>

	<div class="form-container">
		<input class="input-password" placeholder="New Password" id="login-input" type="password" bind:value={password}/>
		<input class="input-password" placeholder="Confirm Password" id="confirm-input" type="password" />

		<button id="unlock-button" on:click={async () => { await loginHandler(); }}>
			{ Boolean(keystore.pwd) ? "Continue Login " : "Get Started " }
			<svg xmlns="http://www.w3.org/2000/svg" style="margin-left: 4px; scale:1.1;" width="16" height="10" viewBox="0 0 16 10" fill="none">
				<path d="M15.4698 5.42426C15.7041 5.18995 15.7041 4.81005 15.4698 4.57574L11.6514 0.757359C11.4171 0.523045 11.0372 0.523045 10.8029 0.757359C10.5686 0.991674 10.5686 1.37157 10.8029 1.60589L14.197 5L10.8029 8.39411C10.5686 8.62843 10.5686 9.00833 10.8029 9.24264C11.0372 9.47696 11.4171 9.47696 11.6514 9.24264L15.4698 5.42426ZM0.954437 5.6H15.0456V4.4H0.954437V5.6Z" fill="white" fill-opacity="0.8"/>
			  </svg>
		</button>
	</div>
</div>
    

<style>

	img {
		width: 160px;
		-webkit-user-drag: none;
		user-select: none;
		-webkit-user-select: none;
	}

	.title-group {
		display: flex;
		align-items:center; justify-content:center;
		flex-direction: column;
		gap:0.5rem;
	}

	.title-group h2 {
		font-size: large;
		font-weight: 400;
		color: #fff;
		margin: 0;
		opacity: 70%;
	}

	#login {
		padding-top: 50px;
		display: flex;
		row-gap: 32px;
		padding-bottom: 1rem;
		flex-direction: column;
		align-items: center;
		background-color: #1A1D26;
		font-size: large;
	}

	.form-container {
		display: flex;
		flex-direction: column;
		align-items: center;
		width: 100%;
	}

	.input-password {
		border-radius: 16px;
		border: 1px solid #313131;
		background: #21242e;
		width: 320px;
		padding: 0.5rem 1rem;
		height: 3rem;
		color: rgba(253, 253, 253, 0.521);
		font-family: Inter;
		font-size:1rem;
		font-style: normal;
		font-weight: 500;
		line-height: 24px;
	}
	
	.input-password:focus {
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
	}

	#unlock-button:hover {
		border: 1px solid #6d6d6d;
	}
</style>