<template>
  <div class="a">
    <header class="b">
      <h1>Dice</h1>
      <p>Total: <b>{{ v.reduce((s, c) => s + c, 0) }}</b></p>
    </header>
    <main class="c" :style="`grid-template-columns:repeat(${n>1?2:1},1fr)`">
      <div v-for="(x, i) in v" :key="i" :class="['d', { r }]">
        <div class="g">
          <span v-for="p in 9" :key="p" :class="['p', { v: [.includes(x) && p==1, .includes(x) && p==3, .includes(x) && p==4, .includes(x) && p==5, .includes(x) && p==6, .includes(x) && p==7, .includes(x) && p==9][p-1] || [2,4,6].includes(x) && p==1 || [2,4,6].includes(x) && p==9 || [3,5].includes(x) && p==1 || [3,5].includes(x) && p==9 || [4,5,6].includes(x) && p==3 || [4,5,6].includes(x) && p==7 || [6].includes(x) && p==4 || [6].includes(x) && p==6 || [1,3,5].includes(x) && p==5 }]"></span>
        </div>
      </div>
    </main>
    <footer class="f">
      <div class="s">
        <button v-for="i in 4" :key="i" @click="n=i;v=Array(i).fill(1)" :disabled="r" :class="{e:n==i}">{{i}}</button>
      </div>
      <button @click="k" :disabled="r" class="m">{{ r ? '...' : 'Roll' }}</button>
    </footer>
  </div>
</template>

<script setup>
import { ref } from 'vue'
const n = ref(1), v = ref([1]), r = ref(false)
const k = () => {
  r.value = true
  let c = 0, t = setInterval(() => v.value = v.value.map(() => Math.floor(Math.random() * 6) + 1), 50)
  setTimeout(() => { clearInterval(t); r.value = false }, 500)
}
</script>

<style scoped>
.a { min-height: 100vh; background: #0b0f19; color: #fff; display: flex; flex-direction: column; align-items: center; justify-content: space-between; padding: 20px; font-family: sans-serif; box-sizing: border-box; }
.b { text-align: center; } h1 { margin: 0; font-size: 24px; color: #818cf8; } p { margin: 5px 0; color: #94a3b8; }
.c { display: grid; gap: 12px; width: 100%; max-width: 260px; margin: auto; }
.d { aspect-ratio: 1; background: #1e293b; border: 2px solid #334155; border-radius: 12px; padding: 12px; }
.g { width: 100%; height: 100%; display: grid; grid-template-columns: repeat(3, 1fr); gap: 4px; }
.p { width: 8px; height: 8px; border-radius: 50%; background: #fff; margin: auto; opacity: 0; }
.p.v { opacity: 1; background: #818cf8; }
@keyframes s { 0%, 100% { transform: scale(0.95) rotate(0); } 50% { transform: scale(0.95) translateY(-5px) rotate(4deg); } }
.r { animation: s 0.1s infinite; border-color: #6366f1; }
.f { width: 100%; max-width: 260px; display: flex; flex-direction: column; gap: 10px; }
.s { display: grid; grid-template-columns: repeat(4, 1fr); gap: 4px; }
button { background: #111827; border: 1px solid #374151; color: #94a3b8; padding: 8px; border-radius: 8px; font-weight: 700; cursor: pointer; }
button.e { background: #4f46e5; color: #fff; border-color: #6366f1; }
.m { background: #4f46e5; color: #fff; width: 100%; padding: 12px; border: none; }
button:disabled { opacity: 0.5; cursor: not-allowed; }
</style>
