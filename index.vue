<template>
  <div class="min-h-screen bg-slate-950 text-slate-100 flex flex-col items-center justify-between p-4 font-sans selection:bg-indigo-500/30">
    <!-- Header -->
    <header class="w-full max-w-md text-center pt-6">
      <div class="flex items-center justify-center gap-2 mb-1">
        <!-- Custom SVG Dice Icon -->
        <svg class="w-8 height-8 text-indigo-400" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
          <rect x="3" y="3" width="18" height="18" rx="3" ry="3"></rect>
          <circle cx="8.5" cy="8.5" r="1.5" fill="currentColor"></circle>
          <circle cx="15.5" cy="8.5" r="1.5" fill="currentColor"></circle>
          <circle cx="15.5" cy="15.5" r="1.5" fill="currentColor"></circle>
          <circle cx="8.5" cy="15.5" r="1.5" fill="currentColor"></circle>
          <circle cx="12" cy="12" r="1.5" fill="currentColor"></circle>
        </svg>
        <h1 class="text-2xl font-black tracking-wider uppercase bg-gradient-to-r from-indigo-400 to-purple-400 bg-clip-text text-transparent">
          Dice Roller
        </h1>
      </div>
      <p class="text-xs text-slate-400">Tap to roll, choose up to 4 dice</p>
    </header>

    <!-- Main Dice Display -->
    <main class="w-full max-w-md flex flex-col items-center justify-center my-auto gap-8">
      <!-- Total Score Display -->
      <div class="text-center h-12">
        <Transition name="scale">
          <div v-if="!isRolling && diceTotal > 0" class="text-sm font-semibold text-slate-400 bg-slate-900/80 px-4 py-1.5 rounded-full border border-slate-800 backdrop-blur-sm shadow-xl inline-block">
            Total Score: <span class="text-indigo-400 font-bold text-base">{{ diceTotal }}</span>
          </div>
        </Transition>
      </div>

      <!-- Dice Container Grid -->
      <div 
        class="grid w-full gap-4 px-4 justify-center items-center"
        :class="{
          'grid-cols-1 max-w-[160px]': diceCount === 1,
          'grid-cols-2 max-w-[320px]': diceCount > 1
        }"
      >
        <div
          v-for="(value, index) in diceValues"
          :key="index"
          class="aspect-square bg-gradient-to-br from-slate-800 to-slate-900 border-2 rounded-2xl p-4 flex flex-col justify-between shadow-2xl relative transition-all duration-300 transform"
          :class="[
            isRolling ? 'animate-bounce border-indigo-500 shadow-indigo-500/10 scale-95 rotate-12' : 'border-slate-700 hover:border-slate-600',
          ]"
        >
          <!-- Custom Dice Face Dot Layouts via Grid Matrix -->
          <div class="w-full h-full grid grid-cols-3 grid-rows-3 gap-1 pointer-events-none">
            <!-- Top Left -->
            <div :class="[dotClass, [2,3,4,5,6].includes(value) ? 'opacity-100' : 'opacity-0']"></div>
            <!-- Top Middle -->
            <div :class="[dotClass, 'opacity-0']"></div>
            <!-- Top Right -->
            <div :class="[dotClass, [4,5,6].includes(value) ? 'opacity-100' : 'opacity-0']"></div>
            
            <!-- Middle Left -->
            <div :class="[dotClass, [6].includes(value) ? 'opacity-100' : 'opacity-0']"></div>
            <!-- Center -->
            <div :class="[dotClass, [1,3,5].includes(value) ? 'opacity-100' : 'opacity-0']"></div>
            <!-- Middle Right -->
            <div :class="[dotClass, [6].includes(value) ? 'opacity-100' : 'opacity-0']"></div>
            
            <!-- Bottom Left -->
            <div :class="[dotClass, [4,5,6].includes(value) ? 'opacity-100' : 'opacity-0']"></div>
            <!-- Bottom Middle -->
            <div :class="[dotClass, 'opacity-0']"></div>
            <!-- Bottom Right -->
            <div :class="[dotClass, [2,3,4,5,6].includes(value) ? 'opacity-100' : 'opacity-0']"></div>
          </div>
        </div>
      </div>
    </main>

    <!-- Controls Footer Panel -->
    <footer class="w-full max-w-md bg-slate-900/60 border border-slate-800/80 backdrop-blur-md rounded-2xl p-5 shadow-2xl mb-4 space-y-5">
      <!-- Quantity Selector Container -->
      <div class="space-y-2">
        <label class="block text-xs font-bold uppercase tracking-wider text-slate-400 text-center">Number of Dice</label>
        <div class="grid grid-cols-4 gap-2">
          <button 
            v-for="n in 4" 
            :key="n"
            @click="setDiceCount(n)"
            :disabled="isRolling"
            class="py-2.5 rounded-xl font-bold border transition-all text-sm"
            :class="[
              diceCount === n 
                ? 'bg-indigo-600 border-indigo-500 text-white shadow-lg shadow-indigo-600/20' 
                : 'bg-slate-800/50 border-slate-700/60 text-slate-300 hover:bg-slate-800'
            ]"
          >
            {{ n }}
          </button>
        </div>
      </div>

      <!-- Action Roll Trigger Button -->
      <button
        @click="rollDice"
        :disabled="isRolling"
        class="w-full py-4 bg-gradient-to-r from-indigo-500 to-purple-600 hover:from-indigo-600 hover:to-purple-700 disabled:from-slate-800 disabled:to-slate-800 text-white font-extrabold text-base tracking-wide rounded-xl shadow-xl active:scale-[0.98] transition-all border border-indigo-400/20 disabled:border-transparent disabled:text-slate-500 disabled:cursor-not-allowed uppercase"
      >
        {{ isRolling ? 'Rolling...' : 'Roll Dice' }}
      </button>
    </footer>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

// App Configuration State
const diceCount = ref(1)
const diceValues = ref([1])
const isRolling = ref(false)

// Reusable styling class for dice pips
const dotClass = 'w-2.5 h-2.5 sm:w-3 sm:h-3 rounded-full bg-gradient-to-r from-indigo-400 to-purple-400 mx-auto my-auto transition-opacity duration-150'

// Computes the summary total dynamically
const diceTotal = computed(() => {
  return diceValues.value.reduce((sum, current) => sum + current, 0)
})

// Configures layout matching user input counts
const setDiceCount = (count) => {
  if (isRolling.value) return
  diceCount.value = count
  diceValues.value = Array(count).fill(1)
}

// Triggers RNG cycle mimicking a physics delay
const rollDice = () => {
  if (isRolling.value) return
  isRolling.value = true

  // Rapidly shuffle values during duration interval
  const interval = setInterval(() => {
    diceValues.value = diceValues.value.map(() => Math.floor(Math.random() * 6) + 1)
  }, 70)

  // Terminate execution and anchor final outcome
  setTimeout(() => {
    clearInterval(interval)
    diceValues.value = diceValues.value.map(() => Math.floor(Math.random() * 6) + 1)
    isRolling.value = false
  }, 600)
}
</script>

<style scoped>
/* Scale animation properties for totals display badge */
.scale-enter-active,
.scale-leave-active {
  transition: all 0.2s ease;
}
.scale-enter-from,
.scale-leave-to {
  opacity: 0;
  transform: scale(0.9);
}

/* Subtle continuous drift style animation injection */
@keyframes bounce {
  0%, 100% { transform: translateY(0) rotate(0deg); }
  50% { transform: translateY(-8px) rotate(4deg); }
}
.animate-bounce {
  animation: bounce 0.15s infinite ease-in-out;
}
</style>
