<script>
    import { getContext } from "svelte";
    const { styleable, builderStore } = getContext("sdk");
    const component = getContext("component");

    export let showDebugInProd;
    export let debugItems;
    export let debugInfos;

    function stringifyAny(obj, options = {}) {
        const {
            indent = 2,
            maxDepth = 10,
            includeFunctions = false,
            includeSymbols = true,
            includeUndefined = true,
        } = options;

        const seen = new WeakSet();

        function _stringify(value, depth = 0) {
            if (depth > maxDepth) return value?.toString();

            if (value === null) return "null";

            switch (typeof value) {
                case "string":
                    return JSON.stringify(value);
                case "number":
                case "boolean":
                    return String(value);
                case "undefined":
                    return includeUndefined ? "undefined" : "";
                case "symbol":
                    return includeSymbols
                        ? `Symbol(${value.description})`
                        : "Symbol";
                case "function":
                    if (!includeFunctions) return "[Function]";
                    return value.toString();
            }

            if (typeof value === "object") {
                if (seen.has(value)) return "[Circular]";
                seen.add(value);
            }

            if (value instanceof Date) {
                return `Date(${value.toISOString()})`;
            }

            if (value instanceof RegExp) {
                return value.toString();
            }

            if (Array.isArray(value)) {
                if (value.length === 0) return "[]";
                const items = value.map((item, i) => {
                    const str = _stringify(item, depth + 1);
                    return indent
                        ? " ".repeat((depth + 1) * indent) + str
                        : str;
                });
                if (indent) {
                    return `[\n${items.join(",\n")}\n${" ".repeat(depth * indent)}]`;
                }
                return `[${items.join(", ")}]`;
            }

            const keys = Object.keys(value).concat(
                includeSymbols ? Object.getOwnPropertySymbols(value) : [],
            );

            if (keys.length === 0) return "{}";

            const entries = keys.map((key) => {
                let keyStr;
                if (typeof key === "symbol") {
                    keyStr = includeSymbols
                        ? `[Symbol(${key.description})]`
                        : "[Symbol]";
                } else if (/^[a-z$_][a-z0-9$_]*$/i.test(key)) {
                    keyStr = key;
                } else {
                    keyStr = JSON.stringify(key);
                }

                const valStr = _stringify(value[key], depth + 1);
                const prefix = indent ? " ".repeat((depth + 1) * indent) : "";
                return `${prefix}${keyStr}: ${valStr}`;
            });

            if (indent) {
                return `{\n${entries.join(",\n")}\n${" ".repeat(depth * indent)}}`;
            }
            return `{${entries.join(", ")}}`;
        }

        try {
            return _stringify(obj);
        } catch (error) {
            return `[Stringify Error: ${error.message}]`;
        }
    }
</script>

{#if $builderStore.inBuilder || showDebugInProd}
    <div use:styleable={$component.styles}>
        {#each debugItems || [] as item}
            {#if item.label}
                <h3>{item.label}</h3>
                <pre class="value">{stringifyAny(item.value)}</pre>
            {:else}
                <p class="error">{item.message}</p>
            {/if}
        {/each}
        {#each debugInfos || [] as item}
            <p>{item}</p>
        {/each}
        {#if !showDebugInProd}
            <p>
                <i class="ph ph-info" /> This message will not be displayed in the
                production environment.
            </p>
        {/if}
    </div>
{/if}

<style>
    .error {
        background: #f98686;
        color: white;
        border-radius: 8px;
        border: 1px solid #ff8080;
        padding: 10px;
    }
    .value {
        max-height: 100px;
        overflow-y: auto;
        background: #eee;
        border-radius: 8px;
        padding: 10px;
        border: 1px solid #e0e0e0;
        line-height: 20px;
    }
</style>
