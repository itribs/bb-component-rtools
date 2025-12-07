<script>
    import Debug from "../lib/Debug.svelte";
    import { onDestroy } from "svelte";

    export let showDebugInProd;
    export let pollerSeed;
    export let pollerInterval;
    export let pollerTrigger;

    let intervalId;

    function clear() {
        if (intervalId) clearInterval(intervalId);
    }

    $: {
        clear();
        if (pollerSeed && pollerInterval > 0) {
            intervalId = setInterval(() => {
                pollerTrigger?.();
            }, pollerInterval);
        }
    }

    $: debugItems = (() => {
        const items = [];
        if (
            pollerSeed != null &&
            pollerSeed !== "" &&
            !Number.isNaN(pollerSeed)
        ) {
            items.push({
                label: "Current Seed",
                value: pollerSeed,
            });
        } else {
            items.push({
                message: "Please set the seed",
            });
        }
        return items;
    })();

    onDestroy(() => {
        clear();
    });
</script>

<Debug
    {showDebugInProd}
    {debugItems}
    debugInfos={[
        "When the seed is updated and has a value, restart polling.",
        "When it has no value, stop polling.",
    ]}
/>
