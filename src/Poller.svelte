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
        return [
            {
                label: "Current Seed",
                value: pollerSeed,
            },
        ];
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
