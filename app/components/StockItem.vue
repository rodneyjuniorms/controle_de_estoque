<template>
  <div class="group bg-white rounded-xl border border-gray-100 p-4 hover:shadow-md hover:border-gray-200 transition-all duration-200">
    <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-4">
      <!-- Informações do Produto -->
      <div class="flex items-start gap-4 flex-1">
        <!-- Ícone/Avatar -->
        <div class="w-12 h-12 rounded-lg flex items-center justify-center flex-shrink-0"
             :class="stockStatusClass">
          <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M20 7l-8-4-8 4m16 0l-8 4m8-4v10l-8 4m0-10L4 7m8 4v10M4 7v10l8 4" />
          </svg>
        </div>

        <!-- Detalhes -->
        <div class="flex-1 min-w-0">
          <h3 class="font-semibold text-gray-900 truncate">{{ item.name }}</h3>
          <div class="flex flex-wrap items-center gap-x-4 gap-y-1 mt-1">
            <span class="text-sm text-gray-500">
              <span class="font-medium text-gray-700">{{ item.qty }}</span> unidades
            </span>
            <span class="text-sm text-gray-500">
              <span class="font-medium text-gray-700">{{ formatCurrency(item.price) }}</span> / un
            </span>
          </div>
        </div>
      </div>

      <!-- Valor Total e Ações -->
      <div class="flex items-center justify-between sm:justify-end gap-4">
        <div class="text-right">
          <p class="text-xs text-gray-500">Valor Total</p>
          <p class="text-lg font-bold text-gray-900">{{ formatCurrency(valorTotal) }}</p>
        </div>

        <button
          @click="$emit('remove', item.id)"
          class="p-2 text-gray-400 hover:text-red-500 hover:bg-red-50 rounded-lg transition-colors"
          title="Remover produto"
        >
          <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" />
          </svg>
        </button>
      </div>
    </div>

    <!-- Badge de Status -->
    <div v-if="item.qty < 5" class="mt-3 pt-3 border-t border-gray-100">
      <span v-if="item.qty === 0" class="inline-flex items-center gap-1 px-2 py-1 bg-red-100 text-red-700 rounded-full text-xs font-medium">
        <svg class="w-3 h-3" fill="currentColor" viewBox="0 0 20 20">
          <path fill-rule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zM8.707 7.293a1 1 0 00-1.414 1.414L8.586 10l-1.293 1.293a1 1 0 101.414 1.414L10 11.414l1.293 1.293a1 1 0 001.414-1.414L11.414 10l1.293-1.293a1 1 0 00-1.414-1.414L10 8.586 8.707 7.293z" clip-rule="evenodd" />
        </svg>
        Sem estoque
      </span>
      <span v-else class="inline-flex items-center gap-1 px-2 py-1 bg-amber-100 text-amber-700 rounded-full text-xs font-medium">
        <svg class="w-3 h-3" fill="currentColor" viewBox="0 0 20 20">
          <path fill-rule="evenodd" d="M8.257 3.099c.765-1.36 2.722-1.36 3.486 0l5.58 9.92c.75 1.334-.213 2.98-1.742 2.98H4.42c-1.53 0-2.493-1.646-1.743-2.98l5.58-9.92zM11 13a1 1 0 11-2 0 1 1 0 012 0zm-1-8a1 1 0 00-1 1v3a1 1 0 002 0V6a1 1 0 00-1-1z" clip-rule="evenodd" />
        </svg>
        Estoque baixo
      </span>
    </div>
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

const stockStatusClass = computed(() => {
  if (props.item.qty === 0) return 'bg-red-100 text-red-600'
  if (props.item.qty < 5) return 'bg-amber-100 text-amber-600'
  return 'bg-primary-100 text-primary-600'
})

function formatCurrency(value: number): string {
  return value.toLocaleString('pt-BR', {
    style: 'currency',
    currency: 'BRL'
  })
}
</script>
