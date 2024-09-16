<script>
	import { onMount, afterUpdate } from "svelte";
	import { setValue } from "../js/settings";

	export let settings;

	async function save() {
		await setValue("settings", settings);
	}
</script>

<div id="settings">
	<div class="separator no-pad"></div>
	<div class="container">
		<div class="input-section">
			<label for="rpc"> Enter RPC URL: </label>
			<input
				class="input-field"
				name="rpc"
				placeholder="RPC URL"
				bind:value={settings.rpc}
			/>
		</div>

		<div class="input-section">
			<label for="num_fmt">Select FMT:</label>
			<select name="num_fmt" class="input-field" bind:value={settings.num_fmt}>
				<option value="1e0">None</option>
				<option value="auto">Auto</option>
				<option value="1e3">k</option>
				<option value="1e6">Mn</option>
				<option value="1e9">Bn</option>
			</select>
		</div>

		<button
			id="save"
			on:click={async () => {
				await save();
			}}
		>
			<img src="../assets/icons/save.svg" alt="Save Settings" />
			Save Changes</button
		>
		<p>Powered by: @Qubic.Solutions</p>
	</div>
</div>

<style>
	#settings {
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
		gap: 0.5rem;
		display: flex;
		flex-direction: column;
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

	input {
		width: 150px;
		background-color: transparent;
		border: none;
	}

	#save {
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

	#save:active {
		background-color: #161616;
	}

    #save img {
        mix-blend-mode: luminosity;
        opacity: 0.8;
        height: 1.25rem;
    }

	#save:hover {
		border: 1px solid #6d6d6d;
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
</style>
