<script>
    import Debug from "../lib/Debug.svelte";
    import { getContext, onDestroy } from "svelte";
    const { Provider } = getContext("sdk");

    export let showDebugInProd;
    export let bindingValue;
    export let bindingChange;
    export let bindingChangeMode = "none";
    export let bindingChangeDelay = 300;

    let oldValue;
    let hasInitialized = false;

    let debounceTimer;
    let lastExecuted = 0;

    $: dataContext = {
        bindingValue,
    };

    $: {
        if (hasInitialized && bindingValue !== oldValue) {
            if (debounceTimer) clearTimeout(debounceTimer);
            if (bindingChangeMode === "debounce") {
                debounceTimer = setTimeout(() => {
                    bindingChange?.({ bindingValue });
                    debounceTimer = null;
                }, bindingChangeDelay);
            } else if (bindingChangeMode === "throttle") {
                const now = Date.now();
                if (now - lastExecuted >= bindingChangeDelay) {
                    bindingChange?.({ bindingValue });
                    lastExecuted = now;
                }
            } else {
                bindingChange?.({ bindingValue });
            }
        }
        oldValue = bindingValue;
        hasInitialized = true;
    }

    $: debugItems = (() => {
        const items = [];
        if (
            bindingValue != null &&
            bindingValue !== "" &&
            !Number.isNaN(bindingValue)
        ) {
            items.push({
                label: "Current Value",
                value: bindingValue,
            });
        } else {
            items.push({
                message: "Please set a value",
            });
        }
        return items;
    })();

    onDestroy(() => {
        if (debounceTimer) {
            clearTimeout(debounceTimer);
            debounceTimer = null;
        }
    });
</script>

<Provider data={dataContext} />
<Debug {showDebugInProd} {debugItems} />
