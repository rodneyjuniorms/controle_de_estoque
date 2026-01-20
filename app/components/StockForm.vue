<template>
  <div class="card mb-6">
    <!-- Header do Form -->
    <div class="flex items-center gap-3 sm:gap-4 mb-6">
      <div class="icon-circle-accent">
        <svg class="w-5 h-5 sm:w-6 sm:h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 6v6m0 0v6m0-6h6m-6 0H6" />
        </svg>
      </div>
      <div>
        <h2 class="text-base sm:text-lg font-bold text-gray-900 dark:text-white">Adicionar Produto</h2>
        <p class="text-xs sm:text-sm text-gray-500 dark:text-gray-400">Preencha os dados do novo produto</p>
      </div>
    </div>

    <form @submit.prevent="onSubmit" class="space-y-4">
      <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
        <!-- Nome do Produto -->
        <div class="sm:col-span-2">
          <label for="name" class="block text-sm font-semibold text-gray-700 dark:text-gray-300 mb-2">
            Nome do Produto
          </label>
          <input
            id="name"
            v-model="name"
            type="text"
            placeholder="Ex: Notebook Dell"
            required
            class="input-field"
          />
        </div>

        <!-- Quantidade -->
        <div>
          <label for="qty" class="block text-sm font-semibold text-gray-700 dark:text-gray-300 mb-2">
            Quantidade
          </label>
          <input
            id="qty"
            v-model.number="qty"
            type="number"
            min="0"
            placeholder="0"
            required
            class="input-field"
          />
        </div>

        <!-- Preço -->
        <div>
          <label for="price" class="block text-sm font-semibold text-gray-700 dark:text-gray-300 mb-2">
            Preço Unitário
          </label>
          <div class="relative">
            <span class="absolute left-4 top-1/2 -translate-y-1/2 text-gray-500 dark:text-gray-400 text-sm font-medium">R$</span>
            <input
              id="price"
              v-model="priceInput"
              @input="handlePriceInput"
              type="text"
              inputmode="numeric"
              placeholder="0,00"
              required
              class="input-field pl-12"
            />
          </div>
        </div>
      </div>

      <!-- Resumo e Botão -->
      <div class="flex flex-col sm:flex-row items-start sm:items-center justify-between gap-4 pt-4 border-t border-gray-100 dark:border-slate-700">
        <div v-if="valorTotal > 0" class="flex items-center gap-2">
          <div class="w-2 h-2 rounded-full bg-gradient-to-r from-emerald-400 to-teal-500 animate-pulse"></div>
          <span class="text-sm text-gray-600 dark:text-gray-400">
            Valor total em estoque:
            <span class="font-bold text-gray-900 dark:text-white">{{ formatCurrency(valorTotal) }}</span>
          </span>
        </div>
        <div v-else class="text-sm text-gray-400 dark:text-gray-500">
          Preencha quantidade e preço para ver o valor total
        </div>

        <button type="submit" :disabled="loading" class="btn-primary w-full sm:w-auto flex items-center justify-center gap-2">
          <svg v-if="loading" class="animate-spin h-5 w-5" fill="none" viewBox="0 0 24 24">
            <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
            <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
          </svg>
          <svg v-else class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 6v6m0 0v6m0-6h6m-6 0H6" />
          </svg>
          {{ loading ? 'Salvando...' : 'Adicionar ao Estoque' }}
        </button>
      </div>
    </form>
  </div>
</template>

<script setup lang="ts">
defineProps<{
  loading?: boolean
}>()

const emit = defineEmits<{
  'add-stock': [payload: { name: string; qty: number; price: number }]
}>()

const name = ref('')
const qty = ref<number | null>(null)
const price = ref<number | null>(null)
const priceInput = ref('')

function formatToBRL(value: number): string {
  return value.toLocaleString('pt-BR', {
    minimumFractionDigits: 2,
    maximumFractionDigits: 2
  })
}

function handlePriceInput(event: Event) {
  const input = event.target as HTMLInputElement
  let digits = input.value.replace(/\D/g, '')

  if (digits === '') {
    priceInput.value = ''
    price.value = null
    return
  }

  const numericValue = parseInt(digits, 10) / 100
  price.value = numericValue
  priceInput.value = formatToBRL(numericValue)
}

const valorTotal = computed(() => {
  if (qty.value && price.value) {
    return qty.value * price.value
  }
  return 0
})

function formatCurrency(value: number): string {
  return value.toLocaleString('pt-BR', {
    style: 'currency',
    currency: 'BRL'
  })
}

function onSubmit() {
  if (!name.value || qty.value === null || price.value === null) return
  emit('add-stock', { name: name.value, qty: qty.value, price: price.value })
  name.value = ''
  qty.value = null
  price.value = null
  priceInput.value = ''
}
</script>
