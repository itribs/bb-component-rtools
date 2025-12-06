<script>
    import Debug from "../lib/Debug.svelte";
    import { getContext } from "svelte";
    const { Provider, API } = getContext("sdk");

    export let showDebugInProd;
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
        if (iteratorDataSource?.type === "provider") {
            return iteratorDataSource.value?.rows;
        } else if (iteratorDataSource?.type === "table") {
            const datasourceId = iteratorDataSource.tableId;
            console.log(API);
            return iteratorDataSource;
        }
        return iteratorDataSource?.data;
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

    let errMsg;
    $: debugItems = (() => {
        const items = [];
        items.push({
            label: "Current Index",
            value: currentIndex,
        });
        if (data) {
            items.push({
                label: "Current Value",
                value: data,
            });
        } else {
            items.push({
                message: "Please set the data",
            });
        }
        return items;
    })();
</script>

<Provider data={dataContext}></Provider>
<Debug
    {showDebugInProd}
    {debugItems}
    debugInfos={["Trigger an action when the index is updated."]}
/>
