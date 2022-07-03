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
    <span class="shadow"></span>
    <span class="label">{label}</span>
</button>

<style lang="scss">
    @import "src/styles/mixins.scss";

    button {
        @include shadow(16px);

        display: flex;
        padding: 10px 40px;
        border-radius: 6px;
        border: none;
        background-color: transparent;
        cursor: pointer;
        position: relative;
        text-align: center;

        &:hover {
            @include shadow(8px);
        }

        &.active {
            @include shadow(2px);

            .shadow {
                @include shadow(8px, true);
            }
        }

        &:active {
            @include shadow(2px);

            .shadow {
                @include shadow(16px, true);
            }
        }

        .label {
            width: 100%;
            text-align: center;
        }

        .shadow {
            @include shadow(0px, true);
            border-radius: 6px;
            position: absolute;
            left: 0;
            right: 0;
            top: 0;
            bottom: 0;
        }
    }
</style>
