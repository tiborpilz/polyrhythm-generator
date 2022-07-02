<script lang="ts">
 import Input from './lib/Input.svelte'
 import IndicatorRow from './lib/IndicatorRow.svelte'

 let valueA = 4;
 let valueB = 3;
 let bpm = 120;

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

 let step = -1;

 function stepForward() {
   step = (step + 1) % lcm || 0;
   setTimeout(() => stepForward(), timeout)
 }

 stepForward();

 function handleValueA(event) {
   valueA = event.detail.value;
 }

 function handleValueB(event) {
   valueB = event.detail.value;
 }
</script>

<main>
  <h1>Polyrhythms</h1>

  <div class="inputs">
    <Input value={valueA} on:input={handleValueA}></Input>
    <Input value={valueB} on:input={handleValueB}></Input>
    <Input value={bpm} on:input={(event) => bpm = event.detail.value}></Input>
  </div>

  <IndicatorRow count={valueA} stepCount={lcm} step={step} />
  <IndicatorRow count={valueB} stepCount={lcm} step={step} />
  <IndicatorRow count={lcm} stepCount={lcm} step={step} />
</main>

<style>
 main {
   text-align: center;
   padding: 1em;
   margin: 0 auto;
 }
</style>
