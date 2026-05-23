<template>
    <div class="grid grid-cols-2 gap-3 sm:grid-cols-4">
        <div class="rounded-2xl bg-red-600 p-4 text-white shadow-sm">
            <p class="text-sm font-medium uppercase tracking-wider opacity-80">熱量</p>
            <p class="mt-1 text-2xl font-bold tabular-nums">{{ kcal }}</p>
            <p class="text-sm opacity-70">kcal</p>
        </div>
        <div class="rounded-2xl p-4 shadow-sm border-2 border-violet-400 bg-violet-400">
            <p class="text-sm font-medium uppercase tracking-wider text-white">蛋白質</p>
            <p class="mt-1 text-2xl font-bold tabular-nums text-white">{{ protein.toFixed(1) }}</p>
            <p class="text-sm text-white">g</p>
        </div>
        <div class="rounded-2xl  p-4 shadow-sm border-2 border-sky-400 bg-sky-400">
            <p class="text-sm font-medium uppercase tracking-wider text-white">碳水</p>
            <p class="mt-1 text-2xl font-bold tabular-nums text-white">{{ carb.toFixed(1) }}</p>
            <p class="text-sm text-white">g</p>
        </div>
        <div class="rounded-2xl  p-4 shadow-sm border-2 border-amber-400 bg-amber-400">
            <p class="text-sm font-medium uppercase tracking-wider text-white">脂肪</p>
            <p class="mt-1 text-2xl font-bold tabular-nums text-white">{{ fat.toFixed(1) }}</p>
            <p class="text-sm text-white">g</p>
        </div>
    </div>
</template>


<script setup lang="ts">
defineProps<{
    kcal: number
    protein: number
    carb: number
    fat: number
}>()
</script>
