<script lang="ts">
    import Indicator from './Indicator.svelte';

    export let count: number;
    export let stepCount: number;
    export let step: number;

    $: indicators = [...Array(count)].map((_, index) => (stepCount / count) * index);
</script>

<div class="indicator-row">
    {#each indicators as indicator}
        <div class="indicator-wrapper">
            <Indicator active={indicator === step} />
        </div>
    {/each}
</div>

<style lang="scss">
    .indicator-row {
        display: flex;
        width: 100%;
        margin-top: 10px;
        margin-bottom: 10px;
    }

    .indicator-wrapper {
        flex: 1;
    }
</style>
