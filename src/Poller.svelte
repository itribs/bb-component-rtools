<script>
    import { getContext, onDestroy } from "svelte";
    const { styleable, builderStore } = getContext("sdk");
    const component = getContext("component");

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
            }, pollerInterval * 1000);
        }
    }

    onDestroy(() => {
        clear();
    });
</script>

{#if $builderStore.inBuilder || showDebugInProd}
    <div use:styleable={$component.styles}>
        <h3>Current Seed</h3>
        <div class="value">
            {pollerSeed ?? ""}
        </div>
        <p>When the seed is updated and has a value, restart polling.</p>
        <p>when it has no value, stop polling.</p>
        {#if !showDebugInProd}
            <p>
                <i class="ph ph-info" /> This message will not be displayed in the
                production environment.
            </p>
        {/if}
    </div>
{/if}

<style>
    .value {
        max-height: 100px;
        overflow-y: auto;
        background: #eee;
        border-radius: 8px;
        padding: 10px;
        border: 1px solid #e0e0e0;
    }
</style>
