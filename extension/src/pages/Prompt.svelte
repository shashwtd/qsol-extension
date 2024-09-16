<script>
    import browser from "webextension-polyfill";
    import { check_password } from "../../../qubic-wasm/pkg/qubic_wasm";
    export let close;
    export let keystore;
    export let password = ""
    let ipassword = ""

    async function loginHandler() {
        if (check_password(keystore, ipassword)) {
            password = ipassword;
            close = true;
        } else {
            ipassword = ""
            await browser.notifications.create("", {
				type: "basic",
				title: "QSOL Wallet Notification",
				iconUrl: browser.runtime.getURL("assets/qbc1024.png"),
				message: "Invalid password provided!"
			});
        }
	}
</script>

<div id=prompt>
    <div id=prompt-limiter>
        <div id=prompt-inner>
            <h2>
                {
                    `Final Confirmation`
                }
            </h2>
            <h3>
                Enter your password to continue
            </h3>
            <input id="pwd-input" placeholder="••••••••••" type="password" bind:value={ipassword}/>

            <div id=action-tab>
                <button on:click={() => { close = true; }} style="background-color: #262626">Cancel</button>
                <button style="background-color: #205022;" on:click={loginHandler}>Submit</button>
            </div>
        </div>
    </div>
</div>

<style>
    #prompt {
        display: block;
        position: absolute;
        background-color: #202024b4;
        width: 100%;
        height: 100%;
    }

    #prompt-limiter {
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100%;
        width: 100%;
    }

    #prompt-inner {
        display: flex;
        flex-direction: column;
        justify-content: space-between;
        align-items: center;
        background-color: #111111;
        border-radius: 20px;
        width: 320px;
        padding: 0.5rem 1rem;
        border: 1px solid #fff2;
        box-shadow: 0 0 10px 0 rgba(0,0,0,0.2);
    }

    #prompt-inner h2 {
        color: #eee;
        font-size: 1.25rem;
        font-weight: 500;
        text-align: center;
        margin: 0;
        margin-top: 0.5rem;
        padding: 0;
    }

    #prompt-inner h3 {
        color: #999;
        font-size: 1rem;
        font-weight: 400;
        text-align: center;
        margin: 0;
        padding: 0;
        margin: 0.5rem 0;
    }

    #pwd-input {
		font-size: large;
		font-family: Verdana;
        background-color: #ffffff07;
		border: 1px solid #fff2;
		border-radius: 0.75rem;
        width: 100%;
        font-size: 1rem;
        padding: 0 1rem;
        margin-top: 1rem;
        height: 2.5rem;
        color: #fff;
	}

    #pwd-input:focus {
        outline: none;
        border: 1px solid #fff4;
    }

    #action-tab {
        display: flex;
        justify-content: space-evenly;
        width: 100%;
        gap: .5rem;
        margin-top: 0.5rem;
    }

    #action-tab button {
        width: max-content;
        padding: 0.5rem 1.5rem;
        border: 2px #fff1 solid;
        border-radius: 0.75rem;
        color: #fff;
        width: 100%;
        text-align: center;
        cursor: pointer;
        font-size: 1rem;
    }

    #action-tab button:hover {
        border: #fff6 2px solid;
    }
</style>