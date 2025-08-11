<script>
  import { createEventDispatcher } from 'svelte';
  import { fade, fly } from 'svelte/transition';
  
  const dispatch = createEventDispatcher();
  
  export let isOpen = false;
  
  let currentStep = 1;
  let phoneNumber = '';
  let userName = '';
  let currentStatus = '';
  
  // References for auto-focus
  let phoneInput;
  let nameInput;
  
  const statusOptions = [
    'High School Student',
    'College Student', 
    'Professional'
  ];
  
  function handlePhoneSubmit() {
    if (phoneNumber.trim()) {
      currentStep = 2;
    }
  }
  
  function handleNameSubmit() {
    if (userName.trim()) {
      currentStep = 3;
    }
  }
  
  function handleStatusSubmit() {
    if (currentStatus) {
      // Send all collected data
      dispatch('userInfoComplete', {
        phone: phoneNumber,
        name: userName,
        status: currentStatus
      });
      closePopup();
    }
  }
  
  function closePopup() {
    isOpen = false;
    currentStep = 1;
    phoneNumber = '';
    userName = '';
    currentStatus = '';
  }
  
  function handleKeydown(event, nextStep) {
    if (event.key === 'Enter') {
      if (nextStep === 2) handlePhoneSubmit();
      else if (nextStep === 3) handleNameSubmit();
      else if (nextStep === 4) handleStatusSubmit();
    }
  }
  
  // Auto-focus logic
  $: if (currentStep === 1 && phoneInput) {
    setTimeout(() => phoneInput.focus(), 100);
  }
  
  $: if (currentStep === 2 && nameInput) {
    setTimeout(() => nameInput.focus(), 100);
  }
</script>

{#if isOpen}
  <div class="fixed inset-0 bg-black/50 z-50 flex items-center justify-center p-4" transition:fade>
    <div class="bg-gradient-to-br from-blue-600 to-purple-700 rounded-2xl p-6 max-w-sm w-full shadow-2xl border-3 border-white/20" transition:fly={{ y: 50, duration: 300 }}>
      
      <!-- Step 1: Phone Number -->
      {#if currentStep === 1}
        <div class="text-center">
          <div class="mb-4">
            <div class="w-16 h-16 bg-yellow-400 rounded-full mx-auto mb-3 flex items-center justify-center border-3 border-white/30 shadow-lg">
              <img src="/ilc-logo.png" alt="ILC Logo" class="w-10 h-10 rendering-pixelated" />
            </div>
            <h2 class="text-xl font-bold text-white mb-2">Welcome to ILC Quiz!</h2>
            <p class="text-white/80 text-sm">Let's start with your phone number</p>
          </div>
          
          <div class="mb-4">
            <input
              bind:this={phoneInput}
              type="tel"
              bind:value={phoneNumber}
              placeholder="Enter your phone number"
              class="w-full px-4 py-3 rounded-lg border-2 border-white/30 bg-white/10 text-white placeholder-white/60 focus:border-yellow-400 focus:outline-none transition-all text-lg text-center"
              on:keydown={(e) => handleKeydown(e, 2)}
            />
          </div>
          
          <button
            on:click={handlePhoneSubmit}
            disabled={!phoneNumber.trim()}
            class="w-full bg-yellow-400 hover:bg-yellow-300 disabled:bg-gray-400 text-gray-800 font-bold py-2 px-4 rounded-lg transition-all transform hover:scale-105 disabled:transform-none shadow-lg text-sm">
            Next →
          </button>
        </div>
      {/if}
      
      <!-- Step 2: Name -->
      {#if currentStep === 2}
        <div class="text-center">
          <div class="mb-4">
            <div class="w-16 h-16 bg-green-400 rounded-full mx-auto mb-3 flex items-center justify-center border-3 border-white/30 shadow-lg">
              <img src="/ilc-logo.png" alt="ILC Logo" class="w-10 h-10 rendering-pixelated" />
            </div>
            <h2 class="text-xl font-bold text-white mb-2">Great!</h2>
            <p class="text-white/80 text-sm">Now tell us your name</p>
          </div>
          
          <div class="mb-4">
            <input
              bind:this={nameInput}
              type="text"
              bind:value={userName}
              placeholder="Enter your name"
              class="w-full px-4 py-3 rounded-lg border-2 border-white/30 bg-white/10 text-white placeholder-white/60 focus:border-green-400 focus:outline-none transition-all text-lg text-center"
              on:keydown={(e) => handleKeydown(e, 3)}
            />
          </div>
          
          <div class="flex gap-3">
            <button
              on:click={() => currentStep = 1}
              class="flex-1 bg-gray-500 hover:bg-gray-400 text-white font-bold py-2 px-4 rounded-lg transition-all transform hover:scale-105 shadow-lg text-sm">
              ← Back
            </button>
            <button
              on:click={handleNameSubmit}
              disabled={!userName.trim()}
              class="flex-1 bg-green-400 hover:bg-green-300 disabled:bg-gray-400 text-gray-800 font-bold py-2 px-4 rounded-lg transition-all transform hover:scale-105 disabled:transform-none shadow-lg text-sm">
              Next →
            </button>
          </div>
        </div>
      {/if}
      
      <!-- Step 3: Current Status -->
      {#if currentStep === 3}
        <div class="text-center">
          <div class="mb-4">
            <div class="w-16 h-16 bg-purple-400 rounded-full mx-auto mb-3 flex items-center justify-center border-3 border-white/30 shadow-lg">
              <img src="/ilc-logo.png" alt="ILC Logo" class="w-10 h-10 rendering-pixelated" />
            </div>
            <h2 class="text-xl font-bold text-white mb-2">Almost Done!</h2>
            <p class="text-white/80 text-sm">What's your current status?</p>
          </div>
          
          <div class="mb-4 space-y-2">
            {#each statusOptions as option}
              <button
                on:click={() => currentStatus = option}
                class="w-full p-3 rounded-lg border-2 transition-all transform hover:scale-105 text-sm {currentStatus === option ? 'border-purple-400 bg-purple-400/20 text-white' : 'border-white/30 bg-white/10 text-white/80 hover:border-purple-400/50 hover:bg-purple-400/10'}"
              >
                {option}
              </button>
            {/each}
          </div>
          
          <div class="flex gap-3">
            <button
              on:click={() => currentStep = 2}
              class="flex-1 bg-gray-500 hover:bg-gray-400 text-white font-bold py-2 px-4 rounded-lg transition-all transform hover:scale-105 shadow-lg text-sm">
              ← Back
            </button>
            <button
              on:click={handleStatusSubmit}
              disabled={!currentStatus}
              class="flex-1 bg-purple-400 hover:bg-purple-300 disabled:bg-gray-400 text-gray-800 font-bold py-2 px-4 rounded-lg transition-all transform hover:scale-105 disabled:transform-none shadow-lg text-sm">
              Continue →
            </button>
          </div>
        </div>
      {/if}
      

      
      <!-- Close button -->
      <button
        on:click={closePopup}
        class="absolute top-4 right-4 text-white/60 hover:text-white transition-colors"
      >
        <span class="text-2xl">×</span>
      </button>
    </div>
  </div>
{/if}

<style>
  input::placeholder {
    color: rgba(255, 255, 255, 0.6);
  }
  
  input:focus::placeholder {
    color: rgba(255, 255, 255, 0.4);
  }
</style>
