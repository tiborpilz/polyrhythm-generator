<script lang="ts">
    export let active;
</script>

<div class="{ active ? 'indicator indicator--active' : 'indicator' }"></div>

<style lang="scss">
 .indicator {
     border-radius: 50%;
     background-color: #445588;

     width: 10px;
     height: 10px;

     &--active {
         background-color: #AABBFF;
     }
 }
</style>
