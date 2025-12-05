<script>
    import { getContext, onDestroy } from "svelte";
    const { styleable } = getContext("sdk");
    const component = getContext("component");

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
            }, pollerInterval * 1000);
        }
    }

    onDestroy(() => {
        clear();
    });
</script>

<div use:styleable={$component.styles}></div>
