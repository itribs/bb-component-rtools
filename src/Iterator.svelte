<script>
    import { getContext } from "svelte";
    const { styleable, Provider } = getContext("sdk");
    const component = getContext("component");

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

<div use:styleable={$component.styles}>
    <Provider data={dataContext}></Provider>
</div>
