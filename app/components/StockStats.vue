<template>
  <div class="grid grid-cols-2 lg:grid-cols-4 gap-4 mb-8">
    <!-- Total de Produtos -->
    <div class="stat-card">
      <div class="flex items-center justify-between">
        <div>
          <p class="text-sm font-medium text-gray-500">Total Produtos</p>
          <p class="text-2xl font-bold text-gray-900 mt-1">{{ totalProdutos }}</p>
        </div>
        <div class="w-12 h-12 bg-primary-100 rounded-full flex items-center justify-center">
          <svg class="w-6 h-6 text-primary-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M20 7l-8-4-8 4m16 0l-8 4m8-4v10l-8 4m0-10L4 7m8 4v10M4 7v10l8 4" />
          </svg>
        </div>
      </div>
    </div>

    <!-- Quantidade Total -->
    <div class="stat-card">
      <div class="flex items-center justify-between">
        <div>
          <p class="text-sm font-medium text-gray-500">Qtd. em Estoque</p>
          <p class="text-2xl font-bold text-gray-900 mt-1">{{ totalQuantidade }}</p>
        </div>
        <div class="w-12 h-12 bg-green-100 rounded-full flex items-center justify-center">
          <svg class="w-6 h-6 text-green-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 7h.01M7 3h5c.512 0 1.024.195 1.414.586l7 7a2 2 0 010 2.828l-7 7a2 2 0 01-2.828 0l-7-7A1.994 1.994 0 013 12V7a4 4 0 014-4z" />
          </svg>
        </div>
      </div>
    </div>

    <!-- Valor Total -->
    <div class="stat-card">
      <div class="flex items-center justify-between">
        <div>
          <p class="text-sm font-medium text-gray-500">Valor Total</p>
          <p class="text-2xl font-bold text-gray-900 mt-1">{{ formatCurrency(valorTotal) }}</p>
        </div>
        <div class="w-12 h-12 bg-amber-100 rounded-full flex items-center justify-center">
          <svg class="w-6 h-6 text-amber-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
          </svg>
        </div>
      </div>
    </div>

    <!-- Estoque Baixo -->
    <div class="stat-card">
      <div class="flex items-center justify-between">
        <div>
          <p class="text-sm font-medium text-gray-500">Estoque Baixo</p>
          <p class="text-2xl font-bold" :class="estoqueBaixo > 0 ? 'text-red-600' : 'text-gray-900'">
            {{ estoqueBaixo }}
          </p>
        </div>
        <div class="w-12 h-12 rounded-full flex items-center justify-center"
             :class="estoqueBaixo > 0 ? 'bg-red-100' : 'bg-gray-100'">
          <svg class="w-6 h-6" :class="estoqueBaixo > 0 ? 'text-red-600' : 'text-gray-400'" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z" />
          </svg>
        </div>
      </div>
      <p v-if="estoqueBaixo > 0" class="text-xs text-red-500 mt-2">
        {{ estoqueBaixo }} item(ns) com menos de 5 unidades
      </p>
    </div>
  </div>
</template>

<script setup lang="ts">
interface Stock {
  id: number
  name: string
  qty: number
  price: number
}

const props = defineProps<{
  items: Stock[]
}>()

const totalProdutos = computed(() => props.items.length)

const totalQuantidade = computed(() =>
  props.items.reduce((acc, item) => acc + item.qty, 0)
)

const valorTotal = computed(() =>
  props.items.reduce((acc, item) => acc + (item.qty * item.price), 0)
)

const estoqueBaixo = computed(() =>
  props.items.filter(item => item.qty < 5).length
)

function formatCurrency(value: number): string {
  return value.toLocaleString('pt-BR', {
    style: 'currency',
    currency: 'BRL'
  })
}
</script>
