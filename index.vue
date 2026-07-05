<template>
  <div class="a">
    <header class="b">
      <h1>Dice Roller</h1>
    </header>
    <main class="c" :style="`grid-template-columns:repeat(${n>1?2:1},1fr)`">
      <div v-for="(x, i) in v" :key="i" :class="['d', { r }]">
        <div class="g">
          <span v-for="p in 9" :key="p" :class="['p', { v: (parseInt('0080a082a82aa8'.substr((x||1)*2,2),16) & (1 << (p-1))) }]"></span>
        </div>
      </div>
    </main>
    <footer class="f">
      <div class="s">
        <button v-for="i in 4" :key="i" @click="setCount(i)" :disabled="r" :class="{e:n==i}">{{i}}</button>
      </div>
      <button @click="k" :disabled="r" class="m">{{ r ? '...' : 'Roll Dice' }}</button>
    </footer>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const n = ref(1)
const v = ref([])
const r = ref(false)

const setCount = (i) => {
  if (r.value) return
  n.value = i
  v.value = Array(i).fill(1)
}

const k = () => {
  if (r.value) return
  r.value = true
  let t = setInterval(() => {
    v.value = v.value.map(() => Math.floor(Math.random() * 6) + 1)
  }, 50)
  setTimeout(() => { 
    clearInterval(t)
    v.value = v.value.map(() => Math.floor(Math.random() * 6) + 1)
    r.value = false 
  }, 500)
}

onMounted(() => {
  setCount(1)
})
</script>

<style scoped>
.a { min-height: 100vh; background: #000; color: #fff; display: flex; flex-direction: column; align-items: center; justify-content: space-between; padding: 24px; font-family: sans-serif; box-sizing: border-box; }
.b { text-align: center; margin-bottom: 10px; } h1 { margin: 0; font-size: 22px; color: #e2e8f0; text-transform: uppercase; letter-spacing: 1px; }
.c { display: grid; gap: 16px; width: 100%; max-width: 280px; margin: auto; }
.d { aspect-ratio: 1; background: #1c1c1e; border: 2px solid #2c2c2e; border-radius: 12px; padding: 16px; box-sizing: border-box; }
.g { width: 100%; height: 100%; display: grid; grid-template-columns: repeat(3, 1fr); gap: 4px; }
.p { width: 20px; height: 20px; border-radius: 50%; background: #000; margin: auto; opacity: 0; transition: opacity 0.1s; }
.p.v { opacity: 1; background: #fff; }
@keyframes s { 0%, 100% { transform: scale(0.95); } 50% { transform: scale(0.95) translateY(-5px) rotate(3deg); } }
.r { animation: s 0.12s infinite linear; border-color: #3a3a3c; }
.f { width: 100%; max-width: 280px; display: flex; flex-direction: column; gap: 12px; }
.s { display: grid; grid-template-columns: repeat(4, 1fr); gap: 6px; }
button { background: #1c1c1e; border: 1px solid #2c2c2e; color: #a1a1aa; padding: 12px; font-weight: 700; cursor: pointer; border-radius: 0; transition: background 0.15s; }
button.e { background: #3a3a3c; color: #fff; border-color: #48484a; }
.m { background: #1c1c1e; border: 1px solid #3a3a3c; color: #fff; width: 100%; padding: 14px; font-weight: 800; text-transform: uppercase; letter-spacing: 0.5px; border-radius: 0; }
.m:hover:not(:disabled) { background: #2c2c2e; }
button:disabled { opacity: 0.3; cursor: not-allowed; }
</style>
