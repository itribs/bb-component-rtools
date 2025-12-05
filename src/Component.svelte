<script>
    import { getContext } from "svelte";
    import BindingProvider from "./BindingProvider.svelte";
    import Binding from "./Binding.svelte";
    import Tabs from "./Tabs.svelte";
    import TabContainer from "./TabContainer.svelte";
    import Iterator from "./Iterator.svelte";
    import Poller from "./Poller.svelte";

    const { styleable } = getContext("sdk");
    const component = getContext("component");

    export let componentType;

    const componentMap = {
        bindingProvider: BindingProvider,
        binding: Binding,
        tabs: Tabs,
        tabContainer: TabContainer,
        iterator: Iterator,
        poller: Poller,
    };

    $: currentComponent = componentType ? componentMap[componentType] : null;
</script>

<div use:styleable={$component.styles}>
    {#if currentComponent}
        <svelte:component this={currentComponent} {...$$props}>
            <slot />
        </svelte:component>
    {/if}
</div>
