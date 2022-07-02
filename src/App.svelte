<script lang="ts">
 import Input from './lib/Input.svelte'
 import IndicatorRow from './lib/IndicatorRow.svelte'
 import ToggleButton from './lib/ToggleButton.svelte'
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

 const getLcm = (a, b) => {
   let max = Math.max(a, b);
   let min = Math.min(a, b);

   let lcm = max;
   while(lcm % min !== 0) {
     lcm += max;
   }
   return lcm;
 };

 $: lcm = getLcm(valueA, valueB);
 $: timeout = (60000 / bpm) * (4/lcm);
 $: indicatorsA = [...Array(valueA)].map((_, index) => (lcm / valueA) * index);
 $: indicatorsB = [...Array(valueB)].map((_, index) => (lcm / valueB) * index);

 function stepForward() {
   if (run) {
     step = (step + 1) % lcm || 0;
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
  <IndicatorRow count={lcm} activeIndex={step} smaller={true} />
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
