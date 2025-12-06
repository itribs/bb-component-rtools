<script>
    import Debug from "../lib/Debug.svelte";
    import { getContext } from "svelte";
    const { Provider } = getContext("sdk");

    export let showDebugInProd;
    export let bindingValue;

    $: dataContext = {
        bindingValue,
    };

    $: debugItems = (() => {
        const items = [];
        if (bindingValue) {
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
