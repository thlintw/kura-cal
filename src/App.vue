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
                    <button v-if="selectedCount > 0" type="button"
                        class="rounded-lg bg-zinc-100 px-3 py-1.5 text-xs font-medium text-zinc-600 transition-colors active:bg-zinc-200"
                        @click="clearAll">
                        清除 ({{ selectedCount }})
                    </button>
                </div>

                <!-- Macro widgets -->
                <div class="mt-3">
                    <MacroWidgets :kcal="totals.kcal" :protein="totals.protein" :carb="totals.carb" :fat="totals.fat" />
                </div>

                <!-- Search -->
                <div class="relative mt-3">
                    <Search class="pointer-events-none absolute left-3 top-1/2 -translate-y-1/2 text-zinc-400"
                        :size="16" />
                    <input v-model="search" type="search" placeholder="搜尋料理..."
                        class="w-full rounded-xl bg-white py-2.5 pl-9 pr-4 text-sm text-zinc-800 shadow-sm border-2 border-zinc-200 placeholder:text-zinc-400 focus:outline-none focus:border-2 focus:border-red-500" />
                </div>

                <!-- Active filter pill -->
                <div v-if="activeFilter" class="mt-2 flex items-center gap-2">
                    <span class="text-xs text-zinc-500">篩選：</span>
                    <span
                        class="inline-flex items-center gap-1 rounded-full bg-red-100 px-2.5 py-0.5 text-sm font-medium text-red-700">
                        {{ activeFilter }}
                        <button type="button" @click="activeFilter = null" aria-label="清除篩選">
                            <X :size="12" />
                        </button>
                    </span>
                </div>
            </div>
        </header>

        <!-- Dish list -->
        <main class="mx-auto max-w-2xl px-4 py-3 pb-28">
            <div v-if="filteredDishes.length === 0" class="py-16 text-center text-sm text-zinc-400">
                找不到符合條件的料理
            </div>
            <div v-else class="space-y-1.5">
                <DishItem v-for="dish in filteredDishes" :key="dish.id" :dish="dish"
                    :quantity="selections[dish.id] ?? 0" @toggle="toggle(dish.id)"
                    @set-qty="(qty) => setQty(dish.id, qty)" />
            </div>
        </main>

        <!-- Floating filter menu -->
        <FilterMenu :active-filter="activeFilter" @set-filter="(kw) => activeFilter = kw" />
    </div>
</template>


<script setup lang="ts">
import { ref, computed, watch, onMounted } from 'vue'
import { Search, X } from '@lucide/vue'
import { dishes } from './data/dishes'
import MacroWidgets from './components/MacroWidgets.vue'
import DishItem from './components/DishItem.vue'
import FilterMenu from './components/FilterMenu.vue'

const STORAGE_KEY = 'kura-cal-v1'

const selections = ref<Record<number, number>>({})
const search = ref('')
const activeFilter = ref<string | null>(null)

onMounted(() => {
    try {
        const saved = localStorage.getItem(STORAGE_KEY)
        if (saved) selections.value = JSON.parse(saved)
    } catch { }
})

watch(selections, (val) => {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(val))
}, { deep: true })

const filteredDishes = computed(() => {
    let list = dishes
    const q = search.value.trim()
    if (q) list = list.filter((d) => d.name.includes(q))
    if (activeFilter.value) {
        const rx = new RegExp(activeFilter.value)
        list = list.filter((d) => rx.test(d.name))
    }
    return list
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
