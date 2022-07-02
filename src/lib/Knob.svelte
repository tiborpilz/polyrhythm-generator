<script lang="ts">
  /*
   * Create a knob component that dispatches a "change" event when it is turned
   * via the mouse or touch.
   */
  import { createEventDispatcher } from 'svelte';
  import { styles } from '../utils/styles';
  import Indicator from './Indicator.svelte';

  const dispatch = createEventDispatcher();

  export let value = 0;
  export let min = 0;
  export let max = 100;
  export let deadzone = 90;
  export let stepcount;
  export let snap = true;

  let knobHandler;
  let initialCursorAngle = 0;
  let initialValueAngle = 0;
  let active = false;

  $: startAngle = (deadzone / 2);
  $: endAngle = 360 - (deadzone / 2);
  $: angleMultiplier = (360 - deadzone) / (max - min);
  $: rotation = `${getAngle(value)}deg`;
  $: xPos = `${getXPos(getAngle(value))}%`
  $: yPos = `${getYPos(getAngle(value))}%`
  $: indicatorValue = (value - min) / (max - min);
  $: steps = getSteps(value, min, max, stepcount);

  function getSteps(value, min, max, stepcount) {
    if (!stepcount) {
      return [];
    }

    const step = (max - min) / (stepcount - 1);

    const length = 1 + (max - min) / step;

    return Array.from({ length }, (_, i) => {
      const stepValue = min + i * step;
      const angle = getAngle(stepValue);
      return {
        active: value >= stepValue,
        xPos: `${getXPos(angle)}%`,
        yPos: `${getYPos(angle)}%`,
        angle,
        stepValue,
      }
    });
  }

  // calculate x position in percentage (-50% to 50%) from angle
  function getXPos(angle: number) {
    return (50 * Math.cos((angle) * Math.PI / 180)) + 50;
  }

  // calculate y position in percentage (-50% to 50%) from angle
  function getYPos(angle: number) {
    return (-50 * Math.sin((angle) * Math.PI / 180)) + 50;
  }

  // get rotation from event cursor position relative to element
  function getRotation(event: MouseEvent | TouchEvent, element: HTMLElement) {
    const { clientX, clientY } = event;
    const { left, top, width, height } = element.getBoundingClientRect();
    const x = clientX - (left + width / 2);
    const y = clientY - (top + height / 2);
    const angle = Math.atan2(y, x) * 180 / Math.PI - 90;

    // console.log(angle)

    return limitAngle(angle);
  }

  // convert angle to value
  function getValue(angle: number) {
    // convert angle to value
    const value = ((angle - startAngle) / angleMultiplier) + min;

    return Math.max(min, Math.min(max, value));
  }

  // convert value to angle
  function getAngle(value: number) {
    return limitAngle((value - min) * angleMultiplier) + startAngle;
  }

  // clamp angle from 90 to 450
  function limitAngle(angle: number) {
    return ((angle + 360) % 360);
  }
  
  function startDrag(event: MouseEvent | TouchEvent) {
    event.preventDefault();
    initialCursorAngle = getRotation(event, knobHandler as HTMLElement);
    initialValueAngle = getAngle(value);
    dispatch("start", { value });
    active = true;
    document.addEventListener("mousemove", drag);
    document.addEventListener("touchmove", drag);
    document.addEventListener("mouseup", stopDrag);
    document.addEventListener("touchend", stopDrag);
  }

  function drag(event: MouseEvent | TouchEvent) {

      event.preventDefault();
    // value = Math.max(0, Math.min(1, (event.clientX - event.target.offsetLeft) /
    //                              event.target.offsetWidth));
    const angle = limitAngle(getRotation(event, knobHandler as HTMLElement));
    const angleChange = angle - initialCursorAngle;

    const newAngle = limitAngle(initialValueAngle + angleChange);

    const value = getValue(newAngle);

    // value = initialValue + valueChange;

    if (snap) {
      const snapValues = steps.map(({ stepValue }) => stepValue);
      const closest = snapValues.reduce((prev, curr) =>
        Math.abs(curr - value) < Math.abs(prev - value) ? curr : prev);
      // const resolution = (max - min) / (stepcount - 1); console.log(resolution);
      // const snappedValue = Math.round(value / resolution) * resolution;
      dispatch("change", { value: closest });
    }

    else {
      dispatch("change", { value });
    }
    // const value = getValue(angle);
    // dispatch("change", { value });
  }

  function stopDrag() {
    document.removeEventListener("mousemove", drag);
    document.removeEventListener("touchmove", drag);
    document.removeEventListener("mouseup", stopDrag);
    document.removeEventListener("touchend", stopDrag);
    active = false;
    dispatch("stop", { value });
  }

  function setValue(value) {
    dispatch("change", { value });
  }
  
</script>

<div class="knob-wrapper">
  <div class="knob" on:mousedown={startDrag} on:touchstart={startDrag} bind:this={knobHandler}>
    <div class="knob-inner" use:styles={{ rotation, xPos, yPos }}>
      <div class="knob-indicator">
        <Indicator useValue={true} value={indicatorValue} />
      </div>
    </div>
  </div>
  {#each steps as { stepValue, active, xPos, yPos }, i}
    <div on:click={setValue(stepValue)} class="step-indicator" use:styles={{ xPos, yPos }}>
      <Indicator active={active} smaller={true} />
    </div>
  {/each}
</div>

<style lang="scss">
  @import "src/styles/mixins.scss";

  .knob-wrapper {
    position: relative;
    width: 150px;
    height: 150px;
    border-radius: 50%;
    cursor: pointer;
  }
  
  .knob {
    @include shadow(2px);

    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 100px;
    height: 100px;
    border-radius: 50%;

    &::after {
      @include shadow(16px, true);
      --rim: 2px;
      content: "";
      position: absolute;
      top: var(--rim);
      left: var(--rim);
      width: calc(100% - var(--rim) * 2);
      height: calc(100% - var(--rim) * 2);
      border-radius: 50%;
      opacity: 0.5;
    }
  }
  
  .knob-inner {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 60px;
    height: 60px;
    border-radius: 50%;
    overflow: none;
    /* transition: transform 0.2s ease-in-out; */
  }

  .knob-indicator {
    position: absolute;
    top: var(--xPos);
    left: var(--yPos);
    transform: translate(-50%, -50%);
  }

  .step-indicator {
    position: absolute;
    top: var(--xPos);
    left: var(--yPos);
    transform: translate(-50%, -50%);
  }
  
  /* .knob:hover { */
  /*   box-shadow: 0 0 0 2px #ccc; */
  /* } */
  
  /* .knob:active { */
  /*   box-shadow: 0 0 0 4px #ccc; */
  /* } */
</style>
