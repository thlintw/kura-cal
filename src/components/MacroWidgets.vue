<script setup lang="ts">
defineProps<{
  kcal: number
  protein: number
  carb: number
  fat: number
}>()
</script>

<template>
  <div class="grid grid-cols-2 gap-3 sm:grid-cols-4">
    <div class="rounded-2xl bg-red-600 p-4 text-white shadow-sm">
      <p class="text-xs font-medium uppercase tracking-wider opacity-80">熱量</p>
      <p class="mt-1 text-2xl font-bold tabular-nums">{{ kcal }}</p>
      <p class="text-xs opacity-70">kcal</p>
    </div>
    <div class="rounded-2xl bg-white p-4 shadow-sm ring-1 ring-zinc-200">
      <p class="text-xs font-medium uppercase tracking-wider text-zinc-400">蛋白質</p>
      <p class="mt-1 text-2xl font-bold tabular-nums text-zinc-800">{{ protein.toFixed(1) }}</p>
      <p class="text-xs text-zinc-400">g</p>
    </div>
    <div class="rounded-2xl bg-white p-4 shadow-sm ring-1 ring-zinc-200">
      <p class="text-xs font-medium uppercase tracking-wider text-zinc-400">碳水</p>
      <p class="mt-1 text-2xl font-bold tabular-nums text-zinc-800">{{ carb.toFixed(1) }}</p>
      <p class="text-xs text-zinc-400">g</p>
    </div>
    <div class="rounded-2xl bg-white p-4 shadow-sm ring-1 ring-zinc-200">
      <p class="text-xs font-medium uppercase tracking-wider text-zinc-400">脂肪</p>
      <p class="mt-1 text-2xl font-bold tabular-nums text-zinc-800">{{ fat.toFixed(1) }}</p>
      <p class="text-xs text-zinc-400">g</p>
    </div>
  </div>
</template>
