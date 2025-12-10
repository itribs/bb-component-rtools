<script>
    import { getContext, setContext } from "svelte";
    import { writable } from "svelte/store";
    const { styleable, Provider } = getContext("sdk");
    const component = getContext("component");

    export let activeTabValue;
    export let tabDestroyOnDeactive;
    export let tabColor = "#555555";
    export let activeTabColor = "#007aff";
    export let tabsAlign = "flex-start";
    export let tabsChange;

    const tabs = writable([]);

    const activeValue = writable(activeTabValue);
    const destroyOnDeactive = writable(tabDestroyOnDeactive);

    $: activeValue.set(activeTabValue);
    $: destroyOnDeactive.set(tabDestroyOnDeactive);

    function select(value) {
        activeValue.set(value);
        tabsChange?.({ currentTabValue: value });
    }

    const register = (id, icon, label, value) => {
        if (!id || !value) {
            return;
        }
        label = label || "Tab Name";
        if (!$tabs.find((tab) => tab.id === id)) {
            tabs.update((arr) => [...arr, { id, icon, label, value }]);
        } else {
            tabs.update((arr) =>
                arr.map((tab) =>
                    tab.id === id ? { id, icon, label, value } : tab,
                ),
            );
        }
    };

    const unregister = (id) => {
        tabs.update((arr) => arr.filter((tab) => tab.id !== id));
    };

    $: dataContext = {
        activeValue: $activeValue,
    };

    setContext("tabs", {
        register,
        unregister,
        activeValue,
        destroyOnDeactive,
    });
</script>

<div use:styleable={$component.styles}>
    <Provider data={dataContext}></Provider>
    {#if $tabs.length > 0}
        <div
            class="tabs-list"
            style="--tab-color: {tabColor}; --tab-active-color: {activeTabColor}; --tabs-align:{tabsAlign}"
        >
            {#each $tabs as tab}
                <button
                    class="tabs-tab {$activeValue === tab.value
                        ? 'is-active'
                        : ''}"
                    on:click={() => select(tab.value)}
                >
                    {#if tab.icon}
                        <i class="ph ph-{tab.icon}"></i>
                    {/if}
                    {tab.label}
                </button>
            {/each}
        </div>
    {:else}
        Please add Tab Container components to define tabs.
    {/if}
    <slot />
</div>

<style>
    .tabs-list {
        --tab-color: #555555;
        --tab-active-color: #007aff;
        --tabs-align: flex-start;
        display: flex;
        align-items: flex-end;
        justify-content: var(--tabs-align);
        margin: 0 0 8px 0;
        padding: 0;
        gap: 25px;
        position: relative;
        border-bottom: 1px solid rgba(0, 0, 0, 0.1);
    }

    .tabs-tab {
        display: inline-flex;
        align-items: center;
        justify-content: center;
        gap: 8px;
        padding: 12px 3px;
        border: none;
        background: none;
        cursor: pointer;
        font-size: 14px;
        line-height: 1.4;
        color: var(--tab-color);
        transition: color 0.2s ease;
        white-space: nowrap;
        outline: none;
        position: relative;
        text-decoration: none;
        margin-bottom: -1px;
    }

    .tabs-tab.is-active {
        color: var(--tab-active-color);
        font-weight: 500;
    }

    .tabs-tab.is-active::after {
        content: "";
        position: absolute;
        bottom: -1px;
        left: 0;
        right: 0;
        height: 2px;
        background-color: var(--tab-active-color);
        border-radius: 1px;
    }

    .tabs-tab i {
        display: inline-flex;
        align-items: center;
        justify-content: center;
        font-size: 16px;
        line-height: 1;
    }
</style>
