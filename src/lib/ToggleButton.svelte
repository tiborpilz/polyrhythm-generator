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

<style lang="scss">
    @import "src/styles/mixins.scss";

    button {
        @include shadow(8px);

        padding: 10px;
        margin: 20px;
        border-radius: 6px;
        border: none;
        background-color: transparent;
        cursor: pointer;

        &:hover {
            @include shadow(5px);
        }

        &:active {
            @include shadow(6px, true);
        }

        &.active {
            @include shadow(3px, true);
        }
    }
</style>
