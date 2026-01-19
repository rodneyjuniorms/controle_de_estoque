<template>
  <div class="card">
    <div class="flex items-center justify-between mb-6">
      <div class="flex items-center gap-3">
        <div class="w-10 h-10 bg-green-100 rounded-full flex items-center justify-center">
          <svg class="w-5 h-5 text-green-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2" />
          </svg>
        </div>
        <div>
          <h2 class="text-lg font-semibold text-gray-900">Lista de Produtos</h2>
          <p class="text-sm text-gray-500">{{ items.length }} produto(s) cadastrado(s)</p>
        </div>
      </div>
    </div>

    <!-- Estado Vazio -->
    <div v-if="items.length === 0" class="text-center py-12">
      <div class="w-16 h-16 bg-gray-100 rounded-full flex items-center justify-center mx-auto mb-4">
        <svg class="w-8 h-8 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M20 7l-8-4-8 4m16 0l-8 4m8-4v10l-8 4m0-10L4 7m8 4v10M4 7v10l8 4" />
        </svg>
      </div>
      <h3 class="text-lg font-medium text-gray-900 mb-1">Nenhum produto cadastrado</h3>
      <p class="text-gray-500">Adicione seu primeiro produto usando o formulário acima.</p>
    </div>

    <!-- Lista de Produtos -->
    <div v-else class="space-y-3">
      <TransitionGroup name="list">
        <StockItem
          v-for="item in items"
          :key="item.id"
          :item="item"
          @remove="$emit('remove', item.id)"
        />
      </TransitionGroup>
    </div>
  </div>
</template>

<script setup lang="ts">
defineProps<{
  items: Array<{ id: number; name: string; qty: number; price: number }>
  loading?: boolean
}>()

defineEmits<{
  remove: [id: number]
}>()
</script>

<style scoped>
.list-enter-active,
.list-leave-active {
  transition: all 0.3s ease;
}

.list-enter-from {
  opacity: 0;
  transform: translateY(-10px);
}

.list-leave-to {
  opacity: 0;
  transform: translateX(20px);
}
</style>
