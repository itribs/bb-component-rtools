<script>
    import { getContext, onDestroy } from "svelte";
    const { styleable, builderStore, Provider } = getContext("sdk");
    const component = getContext("component");

    export let showDebugInProd;
    export let bindingValue;

    $: dataContext = {
        bindingValue,
    };
</script>

<Provider data={dataContext}></Provider>
{#if $builderStore.inBuilder || showDebugInProd}
    <div use:styleable={$component.styles}>
        {#if bindingValue}
            <h3>Current Value</h3>
            <div class="value">
                {#each bindingValue.toString()?.split("\n") || [] as line}
                    {line}<br />
                {/each}
            </div>
            {#if !showDebugInProd}
                <p>
                    <i class="ph ph-info" /> This message will not be displayed in
                    the production environment.
                </p>
            {/if}
        {:else}
            <div class="error">Please set a value</div>
        {/if}
    </div>
{/if}

<style>
    .error {
        color: var(
            --spectrum-semantic-negative-color-default,
            var(--spectrum-global-color-red-500)
        );
        font-size: var(--spectrum-global-dimension-font-size-75);
        font-weight: bold;
    }
    .value {
        max-height: 100px;
        overflow-y: auto;
        background: #eee;
        border-radius: 8px;
        padding: 10px;
        border: 1px solid #e0e0e0;
    }
</style>
