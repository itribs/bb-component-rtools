<script>
    import { getContext } from "svelte";
    const { styleable, builderStore, Provider } = getContext("sdk");
    const component = getContext("component");

    export let showDebugInProd;
    export let iteratorSource;
    export let iteratorDataProvider;
    export let iteratorDataSource;
    export let iteratorIndex = -1;
    export let iteration;

    let currentIndex = -1;
    let currentItem = null;

    $: indexChanged(iteratorIndex);

    $: dataContext = {
        currentIterateIndex: currentIndex,
        currentIterateItem: currentItem,
    };

    $: data = (() => {
        if (iteratorSource === "dataProvider") {
            return iteratorDataProvider?.rows;
        } else if (iteratorSource === "dataSource") {
            return iteratorDataSource?.data;
        }
        return null;
    })();

    function indexChanged(index) {
        currentIndex = parseInt(index, 10);
        if (!data || isNaN(currentIndex) || currentIndex < 0) {
            currentIndex = -1;
            return;
        } else if (currentIndex >= data.length) {
            currentIndex = data.length - 1;
            return;
        }

        currentItem = data[currentIndex];

        !!currentItem &&
            iteration?.({
                currentIterationItem: currentItem,
                currentIterationIndex: currentIndex,
            });
    }
</script>

<Provider data={dataContext}></Provider>
{#if $builderStore.inBuilder || showDebugInProd}
    <div use:styleable={$component.styles}>
        <h3>Current Index</h3>
        <span class="value">
            {currentIndex}
        </span>
        <h3>Current Value</h3>
        <div class="value">
            {#each data?.toString()?.split("\n") || [] as line}
                {line}<br />
            {/each}
        </div>
        <p>Trigger an action when the index is updated.</p>
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
