<script>
    import { getContext, onDestroy } from "svelte";
    const { styleable } = getContext("sdk");
    const component = getContext("component");

    const tabs = getContext("tabs");
    const id = Math.random().toString(36).slice(2, 9);

    export let tabIcon;
    export let tabLabel = "Tab Name";
    export let tabValue;

    let activeValue;
    let destroyOnDeactive;

    const unsubscribeActiveValue = tabs?.activeValue.subscribe((value) => {
        activeValue = value;
    });

    const unsubscribeDestroyOnDeactive = tabs?.destroyOnDeactive.subscribe(
        (value) => {
            destroyOnDeactive = value;
        },
    );

    tabs?.register(id, tabIcon, tabLabel, tabValue);

    $: tabs?.register(id, tabIcon, tabLabel, tabValue);

    onDestroy(() => {
        unsubscribeActiveValue?.();
        unsubscribeDestroyOnDeactive?.();
        tabs?.unregister(id);
    });
</script>

{#if tabValue === activeValue || !destroyOnDeactive}
    <div
        use:styleable={$component.styles}
        class:hidden={tabValue !== activeValue && !destroyOnDeactive}
    >
        {#if !tabs}
            <div class="error">
                Tab container component need to be wrapped in a [Tabs]
            </div>
        {:else}
            <slot />
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
    .hidden {
        display: none !important;
    }
</style>
