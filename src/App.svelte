<script lang="ts">
  import Input from './lib/Input.svelte'
  import IndicatorRow from './lib/IndicatorRow.svelte'
  import ToggleButton from './lib/ToggleButton.svelte'
  import Knob from './lib/Knob.svelte'
  import * as Tone from 'tone';

  let valueA = 4;
  let valueB = 3;
  let bpm = 120;
  let run = false;

  let step = -1;
  let activeA = -1;
  let activeB = -1;

  const synthA = new Tone.Synth().toDestination();
  const synthB = new Tone.Synth().toDestination();

  /**
   * Calculate the greatest common denominator of two numbers*
   */
  function gcd(a: number, b: number): number {
    if (b === 0) {
      return a;
    }
    return gcd(b, a % b);
  }

  /**
   * Calculate the lowest common multiple of two Numbers
   */
  function lcm(a: number, b: number): number {
    return (a * b) / gcd(a, b);
  }

  $: totalSteps = lcm(valueA, valueB);
  $: timeout = (60000 / bpm) * (4/totalSteps);
  $: indicatorsA = [...Array(valueA)].map((_, index) => (totalSteps / valueA) * index);
  $: indicatorsB = [...Array(valueB)].map((_, index) => (totalSteps / valueB) * index);

  function stepForward() {
    if (run) {
      step = (step + 1) % totalSteps || 0;
      setTimeout(() => stepForward(), timeout);
      activeA = indicatorsA.indexOf(step);
      activeB = indicatorsB.indexOf(step);

      if (activeA !== -1) {
        synthA.triggerAttackRelease('C3', '16n');
      }

      if (activeB !== -1) {
        synthB.triggerAttackRelease('G3', '16n');
      }
    }
  }

  stepForward();

  function handleValueA(event) {
    valueA = event.detail.value;
  }

  function handleValueB(event) {
    valueB = event.detail.value;
  }

  function handleRunChange(event) {
    run = event.detail.active;

    if (run) {
      stepForward();
    }

    else {
      step = -1;
    }
  }
</script>

<main>
  <h1>Polyrhythms</h1>

  <div class="inputs">
    <Input value={valueA} min={1} max={8} on:input={handleValueA} label="Count A"></Input>
    <Input value={valueB} min={1} max={8} on:input={handleValueB} label="Count B"></Input>
    <Input value={bpm} min={40} max={200} step={5} on:input={(event) => bpm = event.detail.value} label="BPM"></Input>
  </div>

  <div class="buttons">
    <ToggleButton active={run} on:change={handleRunChange} label="Run" />
  </div>

  <IndicatorRow count={valueA} activeIndex={activeA} />
  <IndicatorRow count={valueB} activeIndex={activeB} />
  <IndicatorRow count={totalSteps} activeIndex={step} smaller={true} />

  <Knob value={bpm} min={40} max={200} on:change={(event) => bpm = event.detail.value} />
</main>

<style lang="scss">
  @import "src/styles/mixins.scss";
  @import "src/styles/variables.scss";

  main {
    text-align: center;
    padding: 1em;
    margin: 0 auto;
    max-width: min(1280px, 80vw);
  }

  .inputs {
    display: flex;
    justify-content: space-between;
  }

  .buttons {
    display: flex;
    margin: 30px 0;
    justify-content: center;
  }

  h1 {
    @include shadow(2px, false, 'text-shadow');
    font-size: 72px;
    text-transform: uppercase;
    font-family: sans-serif;
    font-weight: 800;
    color: $bg-color;
  }
</style>
