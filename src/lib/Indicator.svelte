<script lang="ts">
    export let active: boolean;
    export let smaller: boolean;
</script>

<div class="indicator" class:active={active} class:smaller={smaller}></div>

<style lang="scss">
@import "src/styles/mixins.scss";

$color1: rgba(108, 141, 154, 0.5);
$color2: rgba(255, 255, 255, 0.8);
$size: 2px;
 .indicator {
     @include shadow(2px);

     --size: 10px;

     border-radius: 50%;
     background-image: radial-gradient(#99AABB, darken(#99AABB, 50%));
     position: relative;

     width: var(--size);
     height: var(--size);

     transition: all 0.1s;

     &.active {
         background-image: radial-gradient(lighten(#99AABB, 90%), lighten(#99AABB, 50%));
         &::after {
             content: '';
             position: absolute;
             border-radius: 50%;
             top: 0;
             left: 0;
             width: var(--size);
             height: var(--size);
             box-shadow: 0 0 16px 4px #AABBFF;
         }
     }

     &.smaller {
         @include shadow(1px);
         --size: 4px;
         margin: 3px;
     }
 }
</style>
