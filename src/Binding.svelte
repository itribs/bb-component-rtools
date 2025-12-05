<script>
    import { getContext, onDestroy } from "svelte";
    const { styleable, builderStore } = getContext("sdk");
    const component = getContext("component");
    const binding_provider = getContext("binding-provider");

    export let bindingName;
    export let bindingValue;
    let bindingOldName = bindingName;

    binding_provider?.register(bindingName, bindingValue);

    $: binding_provider?.register(bindingName, bindingValue);

    $: {
        if (bindingName !== bindingOldName) {
            binding_provider?.unregister(bindingOldName);
            bindingOldName = bindingName;
        }
    }

    onDestroy(() => {
        binding_provider?.unregister(bindingName);
    });
</script>

<div use:styleable={$component.styles}>
    {#if !binding_provider}
        <div class="error">
            Binding component need to be wrapped in a [Binding Provider]
        </div>
    {:else if bindingName && $builderStore.inBuilder}
        <div class="placeholder">
            [{bindingName}]
        </div>
    {:else}
        <div class="placeholder">Please set a name</div>
    {/if}
</div>

<style>
    .placeholder {
        color: var(--spectrum-global-color-gray-600);
    }
    .error {
        color: var(
            --spectrum-semantic-negative-color-default,
            var(--spectrum-global-color-red-500)
        );
        font-size: var(--spectrum-global-dimension-font-size-75);
        font-weight: bold;
    }
</style>
