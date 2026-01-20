<template>
  <div class="card mb-6">
    <div class="flex flex-col lg:flex-row gap-4">
      <!-- Campo de Busca -->
      <div class="flex-1 relative">
        <div class="absolute inset-y-0 left-0 pl-4 flex items-center pointer-events-none">
          <svg class="w-5 h-5 text-gray-400 dark:text-gray-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
          </svg>
        </div>
        <input
          v-model="searchTerm"
          type="text"
          placeholder="Buscar produto..."
          class="input-field pl-12"
        />
      </div>

      <!-- Ordenação e Filtro -->
      <div class="flex flex-col sm:flex-row gap-3">
        <!-- Ordenação -->
        <div class="relative">
          <select v-model="sortBy" class="input-field appearance-none pr-10 min-w-[180px]">
            <option value="name">Nome (A-Z)</option>
            <option value="name-desc">Nome (Z-A)</option>
            <option value="qty">Quantidade (Menor)</option>
            <option value="qty-desc">Quantidade (Maior)</option>
            <option value="price">Preço (Menor)</option>
            <option value="price-desc">Preço (Maior)</option>
            <option value="value">Valor Total (Menor)</option>
            <option value="value-desc">Valor Total (Maior)</option>
          </select>
          <div class="absolute inset-y-0 right-0 pr-3 flex items-center pointer-events-none">
            <svg class="w-5 h-5 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7" />
            </svg>
          </div>
        </div>

        <!-- Filtro de Estoque -->
        <div class="relative">
          <select v-model="stockFilter" class="input-field appearance-none pr-10 min-w-[160px]">
            <option value="all">Todos</option>
            <option value="low">Estoque Baixo</option>
            <option value="ok">Estoque OK</option>
            <option value="empty">Sem Estoque</option>
          </select>
          <div class="absolute inset-y-0 right-0 pr-3 flex items-center pointer-events-none">
            <svg class="w-5 h-5 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7" />
            </svg>
          </div>
        </div>
      </div>
    </div>

    <!-- Tags de Filtro Ativo -->
    <Transition name="fade">
      <div v-if="hasActiveFilters" class="flex flex-wrap items-center gap-2 mt-4 pt-4 border-t border-gray-100 dark:border-slate-700">
        <span class="text-sm text-gray-500 dark:text-gray-400 font-medium">Filtros ativos:</span>

        <button
          v-if="searchTerm"
          @click="searchTerm = ''"
          class="inline-flex items-center gap-1.5 px-3 py-1.5 bg-gradient-to-r from-indigo-500/10 to-purple-500/10 dark:from-indigo-500/20 dark:to-purple-500/20 text-indigo-700 dark:text-indigo-300 rounded-full text-xs font-semibold hover:from-indigo-500/20 hover:to-purple-500/20 dark:hover:from-indigo-500/30 dark:hover:to-purple-500/30 transition-all duration-200"
        >
          <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
          </svg>
          "{{ searchTerm }}"
          <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
          </svg>
        </button>

        <button
          v-if="stockFilter !== 'all'"
          @click="stockFilter = 'all'"
          class="inline-flex items-center gap-1.5 px-3 py-1.5 rounded-full text-xs font-semibold transition-all duration-200"
          :class="filterTagClass"
        >
          <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 4a1 1 0 011-1h16a1 1 0 011 1v2.586a1 1 0 01-.293.707l-6.414 6.414a1 1 0 00-.293.707V17l-4 4v-6.586a1 1 0 00-.293-.707L3.293 7.293A1 1 0 013 6.586V4z" />
          </svg>
          {{ stockFilterLabel }}
          <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
          </svg>
        </button>

        <button
          @click="clearFilters"
          class="text-xs text-gray-500 dark:text-gray-400 hover:text-gray-700 dark:hover:text-gray-300 underline underline-offset-2 ml-1 transition-colors"
        >
          Limpar todos
        </button>
      </div>
    </Transition>
  </div>
</template>

<script setup lang="ts">
const searchTerm = defineModel<string>('search', { default: '' })
const sortBy = defineModel<string>('sort', { default: 'name' })
const stockFilter = defineModel<string>('filter', { default: 'all' })

const hasActiveFilters = computed(() =>
  searchTerm.value !== '' || stockFilter.value !== 'all'
)

const stockFilterLabel = computed(() => {
  const labels: Record<string, string> = {
    low: 'Estoque Baixo',
    ok: 'Estoque OK',
    empty: 'Sem Estoque'
  }
  return labels[stockFilter.value] || ''
})

const filterTagClass = computed(() => {
  switch (stockFilter.value) {
    case 'low':
      return 'bg-gradient-to-r from-amber-500/10 to-orange-500/10 dark:from-amber-500/20 dark:to-orange-500/20 text-amber-700 dark:text-amber-300 hover:from-amber-500/20 hover:to-orange-500/20 dark:hover:from-amber-500/30 dark:hover:to-orange-500/30'
    case 'empty':
      return 'bg-gradient-to-r from-red-500/10 to-rose-500/10 dark:from-red-500/20 dark:to-rose-500/20 text-red-700 dark:text-red-300 hover:from-red-500/20 hover:to-rose-500/20 dark:hover:from-red-500/30 dark:hover:to-rose-500/30'
    case 'ok':
      return 'bg-gradient-to-r from-emerald-500/10 to-teal-500/10 dark:from-emerald-500/20 dark:to-teal-500/20 text-emerald-700 dark:text-emerald-300 hover:from-emerald-500/20 hover:to-teal-500/20 dark:hover:from-emerald-500/30 dark:hover:to-teal-500/30'
    default:
      return ''
  }
})

function clearFilters() {
  searchTerm.value = ''
  stockFilter.value = 'all'
  sortBy.value = 'name'
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
  transform: translateY(-5px);
}
</style>
