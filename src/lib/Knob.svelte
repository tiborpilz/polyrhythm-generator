<script lang="ts">
  /*
   * Create a knob component that dispatches a "change" event when it is turned
   * via the mouse or touch.
   */
  import { createEventDispatcher } from 'svelte';
  import { styles } from '../utils/styles';

  const dispatch = createEventDispatcher();

  export let value = 0;
  export let min = 0;
  export let max = 100;
  export let deadzone = 0;

  let knobHandler;
  let initialCursorAngle = 0;
  let initialValueAngle = 0;

  $: startAngle = 90 + (deadzone / 2);
  $: endAngle = 450 - (deadzone / 2);
  $: angleMultiplier = (360 - deadzone) / (max - min);
  $: rotation = `${getAngle(value)}deg`;

  // get rotation from event cursor position relative to element
  function getRotation(event: MouseEvent | TouchEvent, element: HTMLElement) {
    const { clientX, clientY } = event;
    const { left, top, width, height } = element.getBoundingClientRect();
    const x = clientX - (left + width / 2);
    const y = clientY - (top + height / 2);
    const angle = Math.atan2(y, x) * 180 / Math.PI;

    return angle;
  }

  // convert angle to value
  function getValue(angle: number) {
    // convert angle to value
    const value = ((angle - 90) / angleMultiplier) + min;

    return Math.max(min, Math.min(max, value));
  }

  // convert value to angle
  function getAngle(value: number) {
    return limitAngle(value * angleMultiplier);
  }

  // clamp angle from 90 to 450
  function limitAngle(angle: number) {
    return ((angle + 270) % 360) + 90;
  }
  
  function startDrag(event: MouseEvent | TouchEvent) {
    event.preventDefault();
    initialCursorAngle = getRotation(event, knobHandler as HTMLElement);
    initialValueAngle = getAngle(value);
    dispatch("start", { value });
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

    console.log(angle, getValue(angle), startAngle, endAngle, angleMultiplier);

    // value = initialValue + valueChange;

    dispatch("change", { value });
    // const value = getValue(angle);
    // dispatch("change", { value });
  }

  function stopDrag() {
    document.removeEventListener("mousemove", drag);
    document.removeEventListener("touchmove", drag);
    document.removeEventListener("mouseup", stopDrag);
    document.removeEventListener("touchend", stopDrag);
    dispatch("stop", { value });
  }
  
</script>

<div class="knob-wrapper">
  <div class="knob" on:mousedown={startDrag} on:touchstart={startDrag} bind:this={knobHandler}>
    <div class="knob-inner" use:styles={{ rotation }}>
      <div class="knob-indicator"></div>
    </div>
  </div>
</div>

<style lang="scss">
  .knob-wrapper {
    position: relative;
    width: 100px;
    height: 100px;
    border-radius: 50%;
    background: #eee;
    cursor: pointer;
  }
  
  .knob {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 100px;
    height: 100px;
    border-radius: 50%;
    background: #fff;
    box-shadow: 0 0 0 1px #ccc;
    transition: box-shadow 0.2s ease-in-out;
  }
  
  .knob-inner {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%) rotate(var(--rotation));
    width: 80px;
    height: 80px;
    border-radius: 50%;
    background: #333;
    /* transition: transform 0.2s ease-in-out; */
  }

  .knob-indicator {
    position: absolute;
    top: 50%;
    left: 100%;
    transform: translate(-50%, -50%);
    width: 20px;
    height: 20px;
    border-radius: 50%;
    background: #fff;
    box-shadow: 0 0 0 1px #ccc;
    transition: box-shadow 0.2s ease-in-out;
  }
  
  .knob:hover {
    box-shadow: 0 0 0 2px #ccc;
  }
  
  .knob:active {
    box-shadow: 0 0 0 4px #ccc;
  }
</style>
