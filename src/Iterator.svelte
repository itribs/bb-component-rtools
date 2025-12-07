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
    let data;

    $: indexChanged(iteratorIndex);

    $: dataContext = {
        currentIterateIndex: currentIndex,
        currentIterateItem: currentItem,
    };

    $: (async () => {
        switch (iteratorDataSource?.type) {
            case "provider":
                data = iteratorDataSource.value?.rows;
                break;
            case "table":
                data = await API.fetchTableData(iteratorDataSource.tableId);
                break;
            case "query":
                const result = await API.executeQuery(iteratorDataSource?._id);
                data = result?.data;
                break;
            default:
                data = iteratorDataSource?.data;
        }
    })();

    function isIndexable(obj) {
        if (typeof obj === "string") return true;
        if (obj == null) return false;
        if (typeof obj !== "object" && typeof obj !== "function") return false;
        const length = obj.length;
        return (
            typeof length === "number" &&
            length >= 0 &&
            Number.isInteger(length)
        );
    }

    function indexChanged(index) {
        currentIndex = parseInt(index, 10);
        if (!data || isNaN(currentIndex) || currentIndex < 0) {
            currentIndex = -1;
            return;
        } else if (currentIndex >= data.length) {
            currentIndex = data.length - 1;
            return;
        }

        if (!isIndexable(data)) {
            currentIndex = -1;
            return;
        }

        currentItem = data[currentIndex];

        !!currentItem &&
            iteration?.({
                currentIterationItem: currentItem,
                currentIterationIndex: currentIndex,
            });
    }

    $: debugItems = (() => {
        const items = [];
        items.push({
            label: "Current Index",
            value: currentIndex,
        });
        if (data) {
            if (isIndexable(data)) {
                items.push({
                    label: "Current Value",
                    value: data[currentIndex],
                });
            } else {
                items.push({
                    message: "The data is not indexable",
                });
            }
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
