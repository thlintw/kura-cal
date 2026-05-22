<script setup lang="ts">
import { ref, computed, watch, onMounted } from 'vue'
import { dishes } from './data/dishes'
import MacroWidgets from './components/MacroWidgets.vue'
import DishItem from './components/DishItem.vue'

const STORAGE_KEY = 'kura-cal-v1'

// selections: { [dishId]: quantity }
const selections = ref<Record<number, number>>({})
const search = ref('')

onMounted(() => {
  try {
    const saved = localStorage.getItem(STORAGE_KEY)
    if (saved) selections.value = JSON.parse(saved)
  } catch {}
})

watch(selections, (val) => {
  localStorage.setItem(STORAGE_KEY, JSON.stringify(val))
}, { deep: true })

const filteredDishes = computed(() => {
  const q = search.value.trim()
  if (!q) return dishes
  return dishes.filter((d) => d.name.includes(q))
})

const totals = computed(() => {
  let kcal = 0, protein = 0, carb = 0, fat = 0
  for (const [idStr, qty] of Object.entries(selections.value)) {
    if (qty > 0) {
      const dish = dishes.find((d) => d.id === Number(idStr))
      if (dish) {
        kcal += dish.kcal * qty
        protein += dish.protein * qty
        carb += dish.carb * qty
        fat += dish.fat * qty
      }
    }
  }
  return {
    kcal: Math.round(kcal),
    protein: Math.round(protein * 10) / 10,
    carb: Math.round(carb * 10) / 10,
    fat: Math.round(fat * 10) / 10,
  }
})

const selectedCount = computed(() =>
  Object.values(selections.value).filter((q) => q > 0).length
)

function toggle(id: number) {
  if (selections.value[id] > 0) {
    selections.value[id] = 0
  } else {
    selections.value[id] = 1
  }
}

function setQty(id: number, qty: number) {
  selections.value[id] = qty
}

function clearAll() {
  selections.value = {}
}
</script>

<template>
  <div class="min-h-screen bg-zinc-50">
    <!-- Sticky header -->
    <header class="sticky top-0 z-10 bg-zinc-50/90 backdrop-blur-sm">
      <div class="mx-auto max-w-2xl px-4 pt-5 pb-3">
        <!-- Title row -->
        <div class="flex items-center justify-between">
          <div class="flex items-center gap-2">
            <span class="text-2xl">🍣</span>
            <h1 class="text-xl font-bold tracking-tight text-zinc-900">KuraCal</h1>
          </div>
          <button
            v-if="selectedCount > 0"
            type="button"
            class="rounded-lg bg-zinc-100 px-3 py-1.5 text-xs font-medium text-zinc-600 transition-colors active:bg-zinc-200"
            @click="clearAll"
          >
            清除 ({{ selectedCount }})
          </button>
        </div>

        <!-- Macro widgets -->
        <div class="mt-3">
          <MacroWidgets
            :kcal="totals.kcal"
            :protein="totals.protein"
            :carb="totals.carb"
            :fat="totals.fat"
          />
        </div>

        <!-- Search -->
        <div class="relative mt-3">
          <svg class="pointer-events-none absolute left-3 top-1/2 h-4 w-4 -translate-y-1/2 text-zinc-400" viewBox="0 0 20 20" fill="currentColor">
            <path fill-rule="evenodd" d="M9 3.5a5.5 5.5 0 100 11 5.5 5.5 0 000-11zM2 9a7 7 0 1112.452 4.391l3.328 3.329a.75.75 0 11-1.06 1.06l-3.329-3.328A7 7 0 012 9z" clip-rule="evenodd"/>
          </svg>
          <input
            v-model="search"
            type="search"
            placeholder="搜尋料理..."
            class="w-full rounded-xl bg-white py-2.5 pl-9 pr-4 text-sm text-zinc-800 shadow-sm ring-1 ring-zinc-200 placeholder:text-zinc-400 focus:outline-none focus:ring-2 focus:ring-red-500"
          />
        </div>
      </div>
    </header>

    <!-- Dish list -->
    <main class="mx-auto max-w-2xl px-4 py-3 pb-24">
      <div
        v-if="filteredDishes.length === 0"
        class="py-16 text-center text-sm text-zinc-400"
      >
        找不到「{{ search }}」
      </div>
      <div v-else class="space-y-1.5">
        <DishItem
          v-for="dish in filteredDishes"
          :key="dish.id"
          :dish="dish"
          :quantity="selections[dish.id] ?? 0"
          @toggle="toggle(dish.id)"
          @set-qty="(qty) => setQty(dish.id, qty)"
        />
      </div>
    </main>
  </div>
</template>
