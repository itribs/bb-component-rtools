<script>
    import { getContext } from "svelte";
    import BindingProvider from "./BindingProvider.svelte";
    import Binding from "./Binding.svelte";
    import Iterator from "./Iterator.svelte";
    import Tabs from "./Tabs.svelte";
    import TabContainer from "./TabContainer.svelte";

    const { styleable } = getContext("sdk");
    const component = getContext("component");

    export let componentType;

    const componentMap = {
        bindingProvider: BindingProvider,
        binding: Binding,
        iterator: Iterator,
        tabs: Tabs,
        tabContainer: TabContainer,
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
