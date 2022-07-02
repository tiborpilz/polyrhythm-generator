<script lang="ts">
    import Indicator from './Indicator.svelte';

    export let count: number;
    export let activeIndex: number;
    export let smaller: boolean;

    $: indicators = [...Array(count)].map((_, index) => index === activeIndex);
</script>

<div class="indicator-row">
    {#each indicators as indicator}
        <div class="indicator-wrapper">
            <Indicator active={indicator} smaller={smaller} />
        </div>
    {/each}
</div>

<style lang="scss">
    .indicator-row {
        display: flex;
        width: 100%;
        margin: 20px 0;
        justify-content: center;
    }

    .indicator-wrapper {
        flex: 1;
    }
</style>
