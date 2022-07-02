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
>
    <span class="inner">{label}</span>
</button>

<style lang="scss">
    @import "src/styles/mixins.scss";

    button {
        @include shadow(3px);

        display: flex;
        margin: 20px;
        border-radius: 6px;
        border: none;
        background-color: transparent;
        cursor: pointer;
        position: relative;

        &:hover {
            @include shadow(2px);
        }

        &:active {
            @include shadow(2px);

            .inner {
                @include shadow(6px, true);
            }
        }

        &.active {
            @include shadow(2px);

            .inner {
                @include shadow(3px, true);
            }
        }

        .inner {
            @include shadow(0px, true);
            padding: 10px;
            border-radius: 6px;
            margin-top: -3px;
            margin-left: -3px;
            margin-right: -3px;
        }
    }
</style>
