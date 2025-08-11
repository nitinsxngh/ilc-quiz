<script>
  import RadialChart from "./RadialChart.svelte";
  import { store } from "../assets/store.js";
  import { fade } from "svelte/transition";
  import { transition_out } from "svelte/internal";

  $: strings = $store.strings;
  let topTraits = [];

  store.subscribe((value) => {
    calculateTopTraits();
  });

  function calculateTopTraits() {
    // Get the top personality traits based on weightedPoints
    let max = 0;
    let maxNature = "";
    let maxNatures = [];

    if (store.weightedPoints == null) return;

    // First step, order the natures by their score
    let orderedNatures = Object.keys(store.weightedPoints).sort(
      (a, b) => store.weightedPoints[b] - store.weightedPoints[a]
    );

    // Second step, add the natures with the highest score to the array
    let topTraitsList = [];
    for (let nature of orderedNatures) {
      if (store.weightedPoints[nature] >= max) {
        max = store.weightedPoints[nature];
        maxNature = nature;
        topTraitsList.push(maxNature);
      }
    }

    topTraits = topTraitsList.slice(0, 5); // Get top 5 traits
  }

  function restart() {
    location.reload();
  }

  let doTransition = false;
</script>

{#if !doTransition}
  <section transition:fade class="z-50" on:outroend="{() => restart()}">
    <!-- Using Tailwind CSS, build a grid that divides screen in half. The right grid is vertically divied in other two sections -->
    <div class="grid lg:grid-cols-2 h-screen w-screen">
      <!-- Left grid -->
      <div
        class="bg-black/50 flex flex-col flex-wrap justify-end lg:justify-center items-center pt-2">
        <h1 class="text-white text-box select-none p-0 mb-4 w-[80%] lg:w-[90%]">
          {strings["ResultMessage"]}
        </h1>
        <!-- Top personality traits -->
        <div
          class="flex flex-col justify-center items-center w-[90%] gap-2 pointer-events-none select-none pb-4">
          {#each topTraits as trait, index}
            <div class="text-white text-lg font-bold bg-blue-600/50 px-4 py-2 rounded-lg">
              {index + 1}. {trait}
            </div>
          {/each}
        </div>
        <button
          on:click="{() => {
            doTransition = true;
          }}"
          class="text-white select-none text-box p-0 my-4 leading-none lg:h-fit w-[40%] lg:w-[30%] hidden lg:block"
          >{strings["Restart"]}</button>
      </div>
      <!-- Right grid -->
      <div
        class="bg-black/50 flex flex-col flex-wrap justify-start lg:justify-center items-center pb-2">
        <RadialChart class="w-[75%] m-0 p-0" />

        <button
        on:click="{() => {
          doTransition = true;
        }}"
        class="text-white select-none text-box p-0 my-4 w-[75%] lg:w-[30%] block lg:hidden"
        >{strings["Restart"]}</button>
      </div>
    </div>
  </section>
{:else}
  <section transition:fade>
    <div class="fixed right-0 top-0 w-screen h-screen bg-black"></div>
  </section>
{/if}
