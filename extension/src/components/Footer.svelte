<script>
    // import { onMount } from "svelte";
    // import { get_current_tick } from "../../qubic-wasm/pkg/qubic_wasm";

    // export let settings;
    // export let current_tick;

    // onMount(async () => {
    //     setInterval(async () => {
    //         try {
    //             current_tick = await get_current_tick(settings.rpc);
    //         } catch {
    //             console.log("Failed to get current tick");
    //         }
    //     }, 5000)
    // })
</script>

<div id=footer>
    <div>
        Powered by <a target="_blank" href="https://qubic.solutions/">Qubic.solutions</a>
    </div>
    <div>
        Network Tick: 27 IT/s
        <!-- Network Tick: {current_tick} -->
    </div>
</div>

<style>
    #footer {
        position: fixed;   
        bottom:0; 
        left: 0;
        z-index: 60;
        width: 100%;
        display: flex;
        justify-content: space-between;
        align-items: center;
        height: 24px;
        background-color: #1A1D26;
        padding-left: 10px;
        color: #fff5;
        padding-right: 10px;
    }
    #footer div a {
        color: rgb(94, 120, 168);
    }
</style>