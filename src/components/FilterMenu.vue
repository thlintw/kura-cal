<template>
    <!-- Backdrop -->
    <Transition name="fade">
    <div
      v-if="open"
      class="fixed inset-0 z-20 bg-black/30"
      @click="open = false"
    />
  </Transition>

    <div class="fixed bottom-6 left-6 z-30 flex flex-col items-start gap-2">
        <!-- Filter panel -->
        <Transition name="slide-up">
            <div v-if="open" class="mb-1 w-9/12 rounded-2xl bg-white p-4 shadow-xl ring-1 ring-zinc-200">
                <div class="mb-3 flex items-center justify-between">
                    <span class="text-base font-bold text-zinc-700">快速篩選</span>
                    <button v-if="activeFilter" type="button" class="text-sm text-red-500 font-medium"
                        @click="clearFilter">
                        清除篩選
                    </button>
                </div>
                <div class="flex flex-wrap gap-2">
                    <button v-for="f in filters" :key="f.keyword" type="button"
                        class="rounded-full px-3 py-1 text-base font-medium transition-colors" :class="activeFilter === f.keyword
                            ? 'bg-red-600 text-white'
                            : 'bg-zinc-100 text-zinc-700 active:bg-zinc-200'" @click="select(f.keyword)">
                        {{ f.label }}
                    </button>
                </div>
            </div>
        </Transition>

        <!-- Burger button -->
        <button type="button"
            class="flex h-12 w-12 items-center justify-center rounded-full shadow-md transition-colors" :class="activeFilter
                ? 'bg-red-600 text-white'
                : 'bg-white text-zinc-700 border-2 border-zinc-200'" @click="open = !open" aria-label="篩選選單">
            <X v-if="open" :size="20" />
            <Menu v-else :size="20" />
        </button>
    </div>
</template>


<script setup lang="ts">
import { ref } from 'vue'
import { Menu, X } from '@lucide/vue'

const props = defineProps<{
    activeFilter: string | null
}>()

const emit = defineEmits<{
    setFilter: [keyword: string | null]
}>()

const open = ref(false)

const filters = [
    { label: '鮭魚', keyword: '鮭魚' },
    { label: '鮪魚', keyword: '鮪魚' },
    { label: '鰻魚', keyword: '鰻魚' },
    { label: '鯖魚', keyword: '鯖魚' },
    { label: '蝦', keyword: '蝦' },
    { label: '蟹', keyword: '蟹' },
    { label: '帆立貝', keyword: '帆立貝' },
    { label: '海膽', keyword: '海膽' },
    { label: '章魚', keyword: '章魚' },
    { label: '鯛', keyword: '鯛' },
    { label: '鰤魚', keyword: '鰤魚' },
    { label: '豬', keyword: '豬' },
    { label: '牛', keyword: '牛' },
    { label: '甜點', keyword: '蛋糕|冰淇淋|布丁|雪花冰|聖代|果凍|餅|糰子' },
]

function select(keyword: string) {
    emit('setFilter', props.activeFilter === keyword ? null : keyword)
    open.value = false
}

function clearFilter() {
    emit('setFilter', null)
    open.value = false
}
</script>

<style scoped>
.fade-enter-active,
.fade-leave-active {
    transition: opacity 0.15s ease;
}

.fade-enter-from,
.fade-leave-to {
    opacity: 0;
}

.slide-up-enter-active,
.slide-up-leave-active {
    transition: opacity 0.2s ease, transform 0.2s ease;
}

.slide-up-enter-from,
.slide-up-leave-to {
    opacity: 0;
    transform: translateY(8px);
}
</style>
