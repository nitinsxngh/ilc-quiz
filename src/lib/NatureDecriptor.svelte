<script>
  import { onMount } from "svelte";
  import { slide, fade } from "svelte/transition";
  import { store } from "../assets/store.js";

  let descriptions = null;
  let index = 0;
  export let finish = false;
  let startTransition = false;
  let topTraits = [];

  function getDescriptions() {
    let descriptions = [];
    
    // Create generic personality descriptions based on top traits
    if (topTraits && topTraits.length > 0) {
      descriptions.push("Based on your responses, your personality shows strong traits in several areas...");
      descriptions.push("Your top characteristic is " + topTraits[0] + ", which suggests...");
      descriptions.push("You also show strengths in " + topTraits[1] + " and " + topTraits[2] + "...");
      descriptions.push("This combination creates a unique personality profile that...");
      descriptions.push("Understanding these traits can help you in your personal and professional life.");
    } else {
      descriptions.push("Analyzing your personality profile...");
      descriptions.push("Your responses reveal interesting patterns...");
      descriptions.push("Let's explore what makes you unique...");
    }

    return descriptions;
  }

  function onPointerDown()
  {
    index++;

    if(index >= descriptions.length)
    {
      startTransition = true;
      index = descriptions.length - 1;
    }
  }

  store.subscribe((value) => {
    getTopTraits();
  });

  function getTopTraits() {
    let max = 0;
    let maxNature = "";
    let maxNatures = [];

    if (store.weightedPoints == null) return;

    // First step, order the natures by their score
    let orderedNatures = Object.keys(store.weightedPoints).sort(
      (a, b) => store.weightedPoints[b] - store.weightedPoints[a]
    );

    // Second step, add the natures with the highest score to the array
    for (let nature of orderedNatures) {
      if (store.weightedPoints[nature] >= max) {
        max = store.weightedPoints[nature];
        maxNature = nature;
        maxNatures.push(maxNature);
      }
    }

    topTraits = maxNatures.slice(0, 3); // Get top 3 traits for description
    descriptions = getDescriptions();
  }


  let text;
</script>

{#if !startTransition}
<section class="select-none z-50">
  <div
    class="flex flex-col w-screen h-screen text-center justify-center items-center bg-black/[0.65]"
    on:pointerdown="{() => onPointerDown()}"
    transition:fade
    on:outroend="{() => finish = true}">
    {#key index}
    <h1 bind:this={text} class="text-yellow-50 text-base sm:text-xl md:text-4xl pointer-events-none w-[75%] select-none"
    transition:slide>
      {descriptions[index]}
    </h1>
    {/key}
    <div class="arrow-down animate-pulse"></div>
  </div>
</section>
{/if}
