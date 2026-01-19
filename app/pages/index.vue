<template>
  <div class="min-h-screen bg-gray-50">
    <!-- Header -->
    <header class="bg-white border-b border-gray-200 sticky top-0 z-10">
      <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div class="flex items-center justify-between h-16">
          <div class="flex items-center gap-3">
            <div class="w-10 h-10 bg-primary-600 rounded-lg flex items-center justify-center">
              <svg class="w-6 h-6 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M20 7l-8-4-8 4m16 0l-8 4m8-4v10l-8 4m0-10L4 7m8 4v10M4 7v10l8 4" />
              </svg>
            </div>
            <div>
              <h1 class="text-xl font-bold text-gray-900">Controle de Estoque</h1>
              <p class="text-xs text-gray-500 hidden sm:block">Sistema de gerenciamento de produtos</p>
            </div>
          </div>

          <div class="flex items-center gap-4">
            <span v-if="loading" class="text-sm text-primary-600 flex items-center gap-2">
              <svg class="animate-spin h-4 w-4" fill="none" viewBox="0 0 24 24">
                <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
              </svg>
              Sincronizando...
            </span>
            <span class="text-sm text-gray-500 hidden md:block">
              {{ currentDate }}
            </span>
          </div>
        </div>
      </div>
    </header>

    <!-- Main Content -->
    <main class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
      <!-- Error Alert -->
      <div v-if="error" class="mb-6 p-4 bg-red-50 border border-red-200 rounded-lg flex items-center gap-3">
        <svg class="w-5 h-5 text-red-500 flex-shrink-0" fill="currentColor" viewBox="0 0 20 20">
          <path fill-rule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zM8.707 7.293a1 1 0 00-1.414 1.414L8.586 10l-1.293 1.293a1 1 0 101.414 1.414L10 11.414l1.293 1.293a1 1 0 001.414-1.414L11.414 10l1.293-1.293a1 1 0 00-1.414-1.414L10 8.586 8.707 7.293z" clip-rule="evenodd" />
        </svg>
        <p class="text-sm text-red-700">{{ error }}</p>
        <button @click="error = null" class="ml-auto text-red-500 hover:text-red-700">
          <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 20 20">
            <path fill-rule="evenodd" d="M4.293 4.293a1 1 0 011.414 0L10 8.586l4.293-4.293a1 1 0 111.414 1.414L11.414 10l4.293 4.293a1 1 0 01-1.414 1.414L10 11.414l-4.293 4.293a1 1 0 01-1.414-1.414L8.586 10 4.293 5.707a1 1 0 010-1.414z" clip-rule="evenodd" />
          </svg>
        </button>
      </div>

      <!-- Stats Cards -->
      <StockStats :items="stockItems" />

      <!-- Search & Filters -->
      <StockSearch
        v-model:search="searchTerm"
        v-model:sort="sortBy"
        v-model:filter="stockFilter"
      />

      <!-- Form -->
      <StockForm @add-stock="addStock" :loading="loading" />

      <!-- List -->
      <StockList :items="filteredItems" :loading="loading" @remove="removeStock" />
    </main>

    <!-- Footer -->
    <footer class="bg-white border-t border-gray-200 mt-auto">
      <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-4">
        <p class="text-center text-sm text-gray-500">
          Controle de Estoque &copy; {{ new Date().getFullYear() }} - Desenvolvido com Nuxt 4 + Tailwind CSS + Supabase
        </p>
      </div>
    </footer>
  </div>
</template>

<script setup lang="ts">
const { products, loading, error, fetchProducts, addProduct, deleteProduct } = useProducts()

const searchTerm = ref('')
const sortBy = ref('name')
const stockFilter = ref('all')

// Carregar produtos ao montar
onMounted(() => {
  fetchProducts()
})

const currentDate = computed(() => {
  return new Date().toLocaleDateString('pt-BR', {
    weekday: 'long',
    year: 'numeric',
    month: 'long',
    day: 'numeric'
  })
})

// Converter produtos do Supabase para formato do componente
const stockItems = computed(() => {
  return products.value.map(p => ({
    id: p.id,
    name: p.name_product || '',
    qty: p.quantidade || 0,
    price: p.value || 0
  }))
})

const filteredItems = computed(() => {
  let result = [...stockItems.value]

  // Filtro de busca
  if (searchTerm.value) {
    const search = searchTerm.value.toLowerCase()
    result = result.filter(item =>
      item.name.toLowerCase().includes(search)
    )
  }

  // Filtro de estoque
  switch (stockFilter.value) {
    case 'low':
      result = result.filter(item => item.qty > 0 && item.qty < 5)
      break
    case 'ok':
      result = result.filter(item => item.qty >= 5)
      break
    case 'empty':
      result = result.filter(item => item.qty === 0)
      break
  }

  // Ordenação
  result.sort((a, b) => {
    switch (sortBy.value) {
      case 'name':
        return a.name.localeCompare(b.name)
      case 'name-desc':
        return b.name.localeCompare(a.name)
      case 'qty':
        return a.qty - b.qty
      case 'qty-desc':
        return b.qty - a.qty
      case 'price':
        return a.price - b.price
      case 'price-desc':
        return b.price - a.price
      case 'value':
        return (a.qty * a.price) - (b.qty * b.price)
      case 'value-desc':
        return (b.qty * b.price) - (a.qty * a.price)
      default:
        return 0
    }
  })

  return result
})

async function addStock(payload: { name: string; qty: number; price: number }) {
  const total = payload.qty * payload.price
  await addProduct({
    name_product: payload.name,
    quantidade: payload.qty,
    value: payload.price,
    total: total
  })
}

async function removeStock(id: number) {
  await deleteProduct(id)
}
</script>
