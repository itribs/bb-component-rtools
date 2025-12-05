<script>
    import { getContext, setContext } from "svelte";
    const { styleable, Provider } = getContext("sdk");
    const component = getContext("component");

    export let display;
    const bindings = {};

    const register = (name, value) => {
        if (name) {
            bindings[name] = value;
        }
    };

    const unregister = (name) => {
        if (name) {
            delete bindings[name];
        }
    };

    $: dataContext = {
        bindings,
    };

    setContext("binding-provider", { register, unregister });
</script>

<div use:styleable={$component.styles} class:hidden={!display}>
    <Provider data={dataContext}></Provider>
    {#if Object.keys(bindings).length <= 0}
        Please add Binding components to provide bindings.
    {/if}
    <slot />
</div>

<style>
    .hidden {
        display: none !important;
    }
</style>
