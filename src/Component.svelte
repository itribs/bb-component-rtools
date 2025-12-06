<script>
    import { getContext } from "svelte";
    import Binding from "./Binding.svelte";
    import Tabs from "./Tabs.svelte";
    import TabContainer from "./TabContainer.svelte";
    import Iterator from "./Iterator.svelte";
    import Poller from "./Poller.svelte";

    const component = getContext("component");

    export let componentType;

    const componentMap = {
        binding: Binding,
        tabs: Tabs,
        tabContainer: TabContainer,
        iterator: Iterator,
        poller: Poller,
    };

    $: currentComponent = componentType ? componentMap[componentType] : null;
</script>

{#if currentComponent}
    <svelte:component this={currentComponent} {...$$props}>
        <slot />
    </svelte:component>
{/if}
