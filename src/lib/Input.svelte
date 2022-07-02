<script lang="ts">
 import { createEventDispatcher } from 'svelte';

 export let value = 1;
 export let min = 1;
 export let max = 8;

 const dispatch = createEventDispatcher();

 function dispatchValue(event) {
     dispatch('input', { value });
 }
</script>

<input type="number" bind:value={value} on:input={dispatchValue} onClick="this.select()" min={min} max={max}>
