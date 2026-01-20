<template>
  <div class="group relative bg-white dark:bg-slate-800 rounded-2xl border border-gray-100 dark:border-slate-700 p-4 sm:p-5 hover:shadow-xl hover:-translate-y-1 transition-all duration-300 overflow-hidden">
    <!-- Gradient Background on Hover -->
    <div class="absolute inset-0 opacity-0 group-hover:opacity-100 transition-opacity duration-300 pointer-events-none"
         :class="gradientBgClass"></div>

    <div class="relative flex flex-col sm:flex-row sm:items-center justify-between gap-4">
      <!-- Informações do Produto -->
      <div class="flex items-start sm:items-center gap-3 sm:gap-4 flex-1">
        <!-- Ícone/Avatar com gradiente -->
        <div class="flex-shrink-0 w-11 h-11 sm:w-12 sm:h-12 rounded-xl flex items-center justify-center transition-all duration-300"
             :class="iconClass">
          <svg class="w-5 h-5 sm:w-6 sm:h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M20 7l-8-4-8 4m16 0l-8 4m8-4v10l-8 4m0-10L4 7m8 4v10M4 7v10l8 4" />
          </svg>
        </div>

        <!-- Detalhes -->
        <div class="flex-1 min-w-0">
          <h3 class="font-bold text-gray-900 dark:text-white truncate text-sm sm:text-base">{{ item.name }}</h3>
          <div class="flex flex-wrap items-center gap-x-3 sm:gap-x-4 gap-y-1 mt-1">
            <span class="inline-flex items-center gap-1 text-xs sm:text-sm text-gray-500 dark:text-gray-400">
              <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 7h.01M7 3h5c.512 0 1.024.195 1.414.586l7 7a2 2 0 010 2.828l-7 7a2 2 0 01-2.828 0l-7-7A1.994 1.994 0 013 12V7a4 4 0 014-4z" />
              </svg>
              <span class="font-semibold text-gray-700 dark:text-gray-300">{{ item.qty }}</span> un
            </span>
            <span class="inline-flex items-center gap-1 text-xs sm:text-sm text-gray-500 dark:text-gray-400">
              <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
              </svg>
              <span class="font-semibold text-gray-700 dark:text-gray-300">{{ formatCurrency(item.price) }}</span>
            </span>
          </div>
        </div>
      </div>

      <!-- Valor Total e Ações -->
      <div class="flex items-center justify-between sm:justify-end gap-3 sm:gap-4">
        <div class="text-left sm:text-right">
          <p class="text-xs text-gray-500 dark:text-gray-400 font-medium">Valor Total</p>
          <p class="text-base sm:text-lg font-bold bg-gradient-to-r from-gray-900 to-gray-700 dark:from-white dark:to-gray-300 bg-clip-text text-transparent">
            {{ formatCurrency(valorTotal) }}
          </p>
        </div>

        <button
          @click="$emit('remove', item.id)"
          class="p-2.5 text-gray-400 hover:text-white hover:bg-gradient-to-r hover:from-red-500 hover:to-rose-600 rounded-xl transition-all duration-300 hover:shadow-lg hover:shadow-red-500/25"
          title="Remover produto"
        >
          <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" />
          </svg>
        </button>
      </div>
    </div>

    <!-- Badge de Status -->
    <Transition name="fade">
      <div v-if="item.qty < 5" class="relative mt-3 pt-3 border-t border-gray-100 dark:border-slate-700">
        <span v-if="item.qty === 0" class="badge badge-danger">
          <svg class="w-3 h-3" fill="currentColor" viewBox="0 0 20 20">
            <path fill-rule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zM8.707 7.293a1 1 0 00-1.414 1.414L8.586 10l-1.293 1.293a1 1 0 101.414 1.414L10 11.414l1.293 1.293a1 1 0 001.414-1.414L11.414 10l1.293-1.293a1 1 0 00-1.414-1.414L10 8.586 8.707 7.293z" clip-rule="evenodd" />
          </svg>
          Sem estoque
        </span>
        <span v-else class="badge badge-warning">
          <svg class="w-3 h-3" fill="currentColor" viewBox="0 0 20 20">
            <path fill-rule="evenodd" d="M8.257 3.099c.765-1.36 2.722-1.36 3.486 0l5.58 9.92c.75 1.334-.213 2.98-1.742 2.98H4.42c-1.53 0-2.493-1.646-1.743-2.98l5.58-9.92zM11 13a1 1 0 11-2 0 1 1 0 012 0zm-1-8a1 1 0 00-1 1v3a1 1 0 002 0V6a1 1 0 00-1-1z" clip-rule="evenodd" />
          </svg>
          Estoque baixo
        </span>
      </div>
    </Transition>
  </div>
</template>

<script setup lang="ts">
const props = defineProps<{
  item: { id: number; name: string; qty: number; price: number }
}>()

defineEmits<{
  remove: [id: number]
}>()

const valorTotal = computed(() => props.item.qty * props.item.price)

const iconClass = computed(() => {
  if (props.item.qty === 0) {
    return 'bg-gradient-to-br from-red-500 to-rose-600 text-white shadow-lg shadow-red-500/30'
  }
  if (props.item.qty < 5) {
    return 'bg-gradient-to-br from-amber-400 to-orange-500 text-white shadow-lg shadow-amber-500/30'
  }
  return 'bg-gradient-to-br from-emerald-400 to-teal-500 text-white shadow-lg shadow-emerald-500/30'
})

const gradientBgClass = computed(() => {
  if (props.item.qty === 0) {
    return 'bg-gradient-to-br from-red-500/5 to-rose-500/5 dark:from-red-500/10 dark:to-rose-500/10'
  }
  if (props.item.qty < 5) {
    return 'bg-gradient-to-br from-amber-500/5 to-orange-500/5 dark:from-amber-500/10 dark:to-orange-500/10'
  }
  return 'bg-gradient-to-br from-emerald-500/5 to-teal-500/5 dark:from-emerald-500/10 dark:to-teal-500/10'
})

function formatCurrency(value: number): string {
  return value.toLocaleString('pt-BR', {
    style: 'currency',
    currency: 'BRL'
  })
}
</script>

<style scoped>
.fade-enter-active,
.fade-leave-active {
  transition: all 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
