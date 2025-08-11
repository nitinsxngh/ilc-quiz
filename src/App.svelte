<script>
  import QuestionSystem from "./lib/QuestionSystem.svelte";
  import UserInfoPopup from "./lib/UserInfoPopup.svelte";
  import { store, loadData } from "./assets/store.js";
  import { onMount } from "svelte";
  import { fade } from "svelte/transition";
  import { isTouchEnabled, getLanguage } from "./assets/utils.js";
  import { transition_in } from "svelte/internal";

  $store.numQuestions = 24;
  let musicBg = null;
  let loaded = loadData();
  let isPlaying = false;
  let canStart = false;
  let finishedTransition = false;
  let isSpanish = getLanguage() === "es";
  let showUserInfoPopup = true;
  let userInfo = null;

  onMount(() => {
    musicBg.volume = 0.1;
    setTimeout(() => {
      window.scrollTo({ top: document.body.scrollHeight, behavior: "smooth" });
    }, 100);
  });

  // Personality test categories for reference
  let personalityCategories = [
    "MBTI",
    "Big Five", 
    "Holland RIASEC",
    "Learning Styles",
    "Motivation"
  ];
</script>

<audio
  bind:this="{musicBg}"
  src="audio/quiz-music.ogg"
  type="audio/ogg"
  controls="{false}"
  loop
  preload="auto"></audio>

<!-- Personality test categories loaded -->

<section>
  <div
    class="fixed h-screen w-screen justify-center bg-cover brightness-75 infinite-scroll-right">
  </div>
  <div
    class="fixed h-screen w-screen justify-center bg-cover brightness-75 opacity-50 infinite-scroll-left">
  </div>
</section>

{#await loaded}
  <div
    class="flex flex-col justify-center items-center text-center h-screen w-screen bg-black/50 z-10">
    <h1 class="text-8xl text-white animate-pulse">
      {isSpanish ? "Cargando..." : "Loading..."}
    </h1>
  </div>
{:then data}
  <main class="relative" transition:fade>
    <!-- ILC Logo in top left -->
    <div class="absolute top-4 left-4 md:left-16 z-20">
      <img src="/ilc-logo.png" alt="ILC Logo" class="w-16 h-16 rendering-pixelated" />
    </div>
    
    <!-- User Info Popup -->
    <UserInfoPopup 
      isOpen={showUserInfoPopup} 
      on:userInfoComplete={(event) => {
        userInfo = event.detail;
        showUserInfoPopup = false;
        canStart = true;
      }}
    />
    
    <section>
      {#if canStart}
        {#if finishedTransition}
          <div
            class="flex flex-col flex-wrap h-screen justify-end bg-none"
            transition:fade>
            <QuestionSystem dataExt="{data}" />
          </div>
        {/if}
      {:else}
        <div
          class="flex flex-col flex-wrap h-screen justify-end bg-none"
          transition:fade
          on:outroend="{() => {
            if (!isPlaying) {
              musicBg.play();
              isPlaying = true;
            }
            finishedTransition = true;
          }}">
          <div
            class="flex flex-col select-none justify-center items-center text-center h-[45vh] w-screen transition-all duration-300 z-10 {userInfo ? 'bg-black/50 hover:bg-black/60 cursor-pointer' : 'bg-black/30 cursor-not-allowed'}"
            on:click="{() => {
              if (userInfo) {
                canStart = true;
              }
            }}"
            on:keydown>
            <h1 class="text-4xl lg:text-8xl text-white {userInfo ? 'animate-pulse' : 'opacity-50'}">
              {data.strings["NormalMode"]}
            </h1>
            <h1 class="text-2xl lg:text-4xl text-white/75 {userInfo ? '' : 'opacity-50'}">
              {data.strings["10"]}
            </h1>
            {#if !userInfo}
              <p class="text-white/60 text-lg mt-4">Complete user info first</p>
            {/if}
          </div>
          <div
            class="flex flex-col select-none justify-center items-center text-center h-[45vh] w-screen transition-all duration-300 z-10 {userInfo ? 'bg-black/50 hover:bg-black/60 cursor-pointer' : 'bg-black/30 cursor-not-allowed'}"
            on:click="{() => {
              if (userInfo) {
                canStart = true;
                $store.numQuestions = -1;
              }
            }}"
            on:keydown>
            <h1 class="text-4xl lg:text-8xl text-white {userInfo ? 'animate-pulse' : 'opacity-50'}">
              {data.strings["FullMode"]}
            </h1>
            <h1 class="text-2xl lg:text-4xl text-white/75 {userInfo ? '' : 'opacity-50'}">
              {data.strings["63"]}
            </h1>
            {#if !userInfo}
              <p class="text-white/60 text-lg mt-4">Complete user info first</p>
            {/if}
          </div>
          <div
            class="flex flex-col select-none justify-center items-center text-center h-[10vh] w-screen bg-black/50 hover:bg-black/60 transition-all duration-300 z-10"
            on:click="{() => {
              window.open('https://twitter.com/rionisguild', '_blank');
            }}"
            on:keydown>
            <p class="text-sm lg:text-2xl text-white/75 underline">
              {data.strings["Credits"]}
            </p>
            {#if userInfo}
              <p class="text-xs text-white/60 mt-1">Welcome, {userInfo.name}!</p>
            {/if}
          </div>
        </div>
      {/if}
    </section>
  </main>
{:catch error}
  <div
    class="flex flex-col justify-center items-center text-center h-screen w-screen bg-black/50 z-10"
    transition:fade>
    <h1 class="text-8xl text-white animate-pulse">
      {isSpanish ? "Error al cargar :(" : "Error promise rejected :("}
      {error.message}
    </h1>
  </div>
{/await}
