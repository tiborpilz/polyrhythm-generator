<script lang="ts">
 import { createEventDispatcher } from 'svelte';

 export let value = 1;
 export let min;
 export let max;
 export let step;
 export let label='';

 const dispatch = createEventDispatcher();

 function dispatchValue(event) {
     dispatch('input', { value });
 }
</script>

<div class="input">
    <label>
        <span class="labeltext">{label}</span>
        <input type="number" bind:value={value} on:input={dispatchValue} onClick="this.select()" min={min} max={max} step={step}>
    </label>
</div>

<style lang="scss">
    @import "src/styles/mixins.scss";

    label {
        position: relative;
        display: flex;
        flex-direction: column;
    }

    input {
        @include shadow(2px, true);

        padding: 10px;
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
            @include shadow(4px, true);
        }
    }

    .labeltext {
        align-self: flex-start;
    }
</style>
