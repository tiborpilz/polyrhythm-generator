<script lang="ts">
  import { styles } from '../utils/styles';
  export let active: boolean;
  export let useValue: boolean;
  export let value: number;
  export let smaller: boolean;

  $: value = useValue ? value : Number(active);
  $: lightOpacity = value * 2;
  $: shadowSize = (Math.max(0, value - 0.5) * 2) ** 2;
</script>

<div class="indicator" class:active={value > 0.8} class:smaller={smaller}
     use:styles={{ lightOpacity, shadowSize }}></div>

<style lang="scss">
  @import "src/styles/mixins.scss";
  @import "src/styles/variables.scss";

  $color1: rgba(108, 141, 154, 0.5);
  $color2: rgba(255, 255, 255, 0.8);
  $size: 2px;
  .indicator {
    @include shadow(2px);
    @include light();

    --size: 10px;

    border-radius: 50%;
    /* background-image: radial-gradient(#99AABB, darken(#99AABB, 50%)); */
    position: relative;

    width: var(--size);
    height: var(--size);

    &::before, &::after {
      content: '';
      position: absolute;
      border-radius: 50%;
      top: 0;
      left: 0;
      width: var(--size);
      height: var(--size);
      transition: all 0.4s ease;
    }

    &::before {
      background-image: radial-gradient(lighten($accent-color, 0%), darken($accent-color, 20%));
      /* transition: all 0.3s ease-out; */

      opacity: var(--lightOpacity);
    }

    &::after {
      @include light-glow(true);
      background-image: radial-gradient(lighten($accent-color, 50%), lighten($accent-color, 20%));

      opacity: calc(var(--lightOpacity) - 0.5);
    }

    &.active::before, &.active::after {
      transition: all 0.1s ease;
    }

    &.smaller {
      @include shadow(1px);
      --size: 4px;
      margin: 3px;
    }
  }
</style>
