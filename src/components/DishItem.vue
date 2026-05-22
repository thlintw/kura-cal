<template>
    <div class="flex items-center gap-3 rounded-xl px-3 py-3 transition-colors"
        :class="checked() ? 'bg-red-50' : 'bg-white'">
        <!-- Checkbox -->
        <button type="button"
            class="flex h-6 w-6 shrink-0 items-center justify-center rounded-md border-2 transition-colors" :class="checked()
                ? 'border-red-600 bg-red-600 text-white'
                : 'border-zinc-300 bg-white text-transparent'" @click="emit('toggle')"
            :aria-label="`${checked() ? '取消選擇' : '選擇'} ${dish.name}`">
            <Check :size="12" stroke-width="3" />
        </button>

        <!-- Dish info -->
        <div class="min-w-0 flex-1">
            <p class="truncate text-sm font-medium text-zinc-800">{{ dish.name }}</p>
            <p class="text-xs text-zinc-400">{{ dish.kcal }} kcal &middot; P {{ dish.protein }}g &middot; C {{ dish.carb
                }}g &middot; F {{ dish.fat }}g</p>
        </div>

        <!-- Quantity stepper (only when checked) -->
        <div v-if="checked()" class="flex shrink-0 items-center gap-1">
            <button type="button"
                class="flex h-7 w-7 items-center justify-center rounded-lg bg-zinc-100 text-zinc-600 transition-colors active:bg-zinc-200"
                @click="decrement" aria-label="減少數量">
                <Minus :size="14" />
            </button>
            <span class="w-8 text-center text-sm font-semibold tabular-nums text-zinc-800">{{ quantity }}</span>
            <button type="button"
                class="flex h-7 w-7 items-center justify-center rounded-lg bg-zinc-100 text-zinc-600 transition-colors active:bg-zinc-200"
                @click="increment" aria-label="增加數量">
                <Plus :size="14" />
            </button>
        </div>
    </div>
</template>


<script setup lang="ts">
import { Check, Minus, Plus } from '@lucide/vue'
import type { Dish } from '../data/dishes'

const props = defineProps<{
    dish: Dish
    quantity: number
}>()

const emit = defineEmits<{
    toggle: []
    setQty: [qty: number]
}>()

const checked = () => props.quantity > 0

function decrement() {
    const next = Math.round((props.quantity - 0.5) * 10) / 10
    if (next <= 0) emit('toggle')
    else emit('setQty', next)
}

function increment() {
    const next = Math.round((props.quantity + 0.5) * 10) / 10
    emit('setQty', next)
}
</script>