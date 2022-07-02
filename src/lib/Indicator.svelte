<script lang="ts">
    export let active: boolean;
</script>

<div class="{ active ? 'indicator indicator--active' : 'indicator' }"></div>

<style lang="scss">
@import "src/styles/mixins.scss";

$color1: rgba(108, 141, 154, 0.5);
$color2: rgba(255, 255, 255, 0.8);
$size: 2px;
 .indicator {
     @include shadow(2px);

     border-radius: 50%;
     background-image: radial-gradient(#99AABB, darken(#99AABB, 50%));
     position: relative;
     /* background-image: radial-gradiant(red, green, blue); */
     /* background-color: #99AABB; */
     /* box-shadow: 0 0 0 0 #445588,
        inset 0 0 4px #000,
        $size $size $size * 2 0 $color1,
        -1 * $size -1 * $size $size * 2 0 $color2; */

     width: 10px;
     height: 10px;

     transition: all 0.1s;

     &--active {
         background-image: radial-gradient(lighten(#99AABB, 90%), lighten(#99AABB, 50%));
         &::after {
             content: '';
             position: absolute;
             border-radius: 50%;
             top: 0;
             left: 0;
             width: 10px;
             height: 10px;
             box-shadow: 0 0 16px 4px #AABBFF;
         }
     }
 }
</style>
