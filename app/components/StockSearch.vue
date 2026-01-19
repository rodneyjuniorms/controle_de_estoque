<template>
  <div class="card mb-6">
    <div class="flex flex-col sm:flex-row gap-4">
      <!-- Campo de Busca -->
      <div class="flex-1 relative">
        <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
          <svg class="w-5 h-5 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
          </svg>
        </div>
        <input
          v-model="searchTerm"
          type="text"
          placeholder="Buscar produto..."
          class="input-field pl-10"
        />
      </div>

      <!-- Ordenação -->
      <div class="sm:w-48">
        <select v-model="sortBy" class="input-field">
          <option value="name">Nome (A-Z)</option>
          <option value="name-desc">Nome (Z-A)</option>
          <option value="qty">Quantidade (Menor)</option>
          <option value="qty-desc">Quantidade (Maior)</option>
          <option value="price">Preço (Menor)</option>
          <option value="price-desc">Preço (Maior)</option>
          <option value="value">Valor Total (Menor)</option>
          <option value="value-desc">Valor Total (Maior)</option>
        </select>
      </div>

      <!-- Filtro de Estoque -->
      <div class="sm:w-44">
        <select v-model="stockFilter" class="input-field">
          <option value="all">Todos</option>
          <option value="low">Estoque Baixo</option>
          <option value="ok">Estoque OK</option>
          <option value="empty">Sem Estoque</option>
        </select>
      </div>
    </div>

    <!-- Tags de Filtro Ativo -->
    <div v-if="hasActiveFilters" class="flex flex-wrap gap-2 mt-4 pt-4 border-t border-gray-100">
      <span class="text-sm text-gray-500">Filtros ativos:</span>

      <button
        v-if="searchTerm"
        @click="searchTerm = ''"
        class="inline-flex items-center gap-1 px-2 py-1 bg-primary-100 text-primary-700 rounded-full text-xs font-medium hover:bg-primary-200 transition-colors"
      >
        Busca: "{{ searchTerm }}"
        <svg class="w-3 h-3" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
        </svg>
      </button>

      <button
        v-if="stockFilter !== 'all'"
        @click="stockFilter = 'all'"
        class="inline-flex items-center gap-1 px-2 py-1 bg-amber-100 text-amber-700 rounded-full text-xs font-medium hover:bg-amber-200 transition-colors"
      >
        {{ stockFilterLabel }}
        <svg class="w-3 h-3" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
        </svg>
      </button>

      <button
        @click="clearFilters"
        class="text-xs text-gray-500 hover:text-gray-700 underline ml-2"
      >
        Limpar todos
      </button>
    </div>
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

function clearFilters() {
  searchTerm.value = ''
  stockFilter.value = 'all'
  sortBy.value = 'name'
}
</script>
