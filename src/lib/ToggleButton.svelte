<script lang="ts">
    import { createEventDispatcher } from 'svelte';

    export let active = false;
    export let label = '';

    const dispatch = createEventDispatcher();

    function dispatchToggle() {
        dispatch('change', { active: !active });
    }
</script>

<button
    class="{active ? 'active' : ''}"
    on:click={dispatchToggle}
>{label}</button>
