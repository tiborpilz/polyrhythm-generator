<script lang="ts">
    export let active: boolean;
</script>

<div class="{ active ? 'indicator indicator--active' : 'indicator' }"></div>

<style lang="scss">
$color1: rgba(108, 141, 154, 0.5);
$color2: rgba(255, 255, 255, 0.8);
$size: 2px;
 .indicator {
     border-radius: 50%;
     background-color: #99AABB;
     box-shadow: 0 0 0 0 #445588,
                 inset 0 0 4px #000,
                 $size $size $size * 2 0 $color1,
                 -1 * $size -1 * $size $size * 2 0 $color2;

     width: 10px;
     height: 10px;

     transition: all 0.1s;

     &--active {
         background-color: #FFF;
         box-shadow: 0 0 16px 4px #AABBFF,
                     inset 0 0 2px #AABBFF;
     }
 }
</style>
