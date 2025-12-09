<script>
    import Debug from "../lib/Debug.svelte";
    import { getContext } from "svelte";
    const { Provider } = getContext("sdk");

    export let showDebugInProd;
    export let bindingValue;
    export let bindingChange;

    let oldValue;
    let hasInitialized = false;

    $: dataContext = {
        bindingValue,
    };

    $: {
        if (hasInitialized && bindingValue !== oldValue) {
            bindingChange?.({ bindingValue });
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
</script>

<Provider data={dataContext} />
<Debug {showDebugInProd} {debugItems} />
