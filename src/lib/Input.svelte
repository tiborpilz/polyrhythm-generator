<script lang="ts">
 import { createEventDispatcher } from 'svelte';

 export let value = 1;
 export let min;
 export let max;
 export let step;

 const dispatch = createEventDispatcher();

 function dispatchValue(event) {
     dispatch('input', { value });
 }
</script>

<input type="number" bind:value={value} on:input={dispatchValue} onClick="this.select()" min={min} max={max} step={step}>

<style lang="scss">
    @import "src/styles/mixins.scss";

    input {
        @include shadow(8px);

        padding: 10px;
        margin: 20px;
        border-radius: 6px;
        outline: none;
        border: none;
        background-color: transparent;
        text-align: center;

        &::selection,
        &::-moz-selection {
            background: rgba(255,255,255,0.4);
        }

        &:focus {
            @include shadow(16px);
        }
    }
</style>
