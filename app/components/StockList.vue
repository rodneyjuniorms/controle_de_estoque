<template>
  <div class="card">
    <!-- Header da Lista -->
    <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-4 mb-6">
      <div class="flex items-center gap-3 sm:gap-4">
        <div class="icon-circle-success">
          <svg class="w-5 h-5 sm:w-6 sm:h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2" />
          </svg>
        </div>
        <div>
          <h2 class="text-base sm:text-lg font-bold text-gray-900 dark:text-white">Lista de Produtos</h2>
          <p class="text-xs sm:text-sm text-gray-500 dark:text-gray-400">
            {{ items.length }} produto(s) cadastrado(s)
          </p>
        </div>
      </div>

      <!-- Contador com gradiente -->
      <div v-if="items.length > 0" class="flex items-center gap-2">
        <div class="px-4 py-2 rounded-xl bg-gradient-to-r from-indigo-500/10 to-purple-500/10 dark:from-indigo-500/20 dark:to-purple-500/20">
          <span class="text-sm font-bold bg-gradient-to-r from-indigo-600 to-purple-600 dark:from-indigo-400 dark:to-purple-400 bg-clip-text text-transparent">
            {{ items.length }} itens
          </span>
        </div>
      </div>
    </div>

    <!-- Estado Vazio -->
    <div v-if="items.length === 0" class="text-center py-12 sm:py-16">
      <div class="w-20 h-20 sm:w-24 sm:h-24 mx-auto mb-6 rounded-2xl bg-gradient-to-br from-gray-100 to-gray-200 dark:from-slate-700 dark:to-slate-800 flex items-center justify-center">
        <svg class="w-10 h-10 sm:w-12 sm:h-12 text-gray-400 dark:text-gray-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M20 7l-8-4-8 4m16 0l-8 4m8-4v10l-8 4m0-10L4 7m8 4v10M4 7v10l8 4" />
        </svg>
      </div>
      <h3 class="text-lg sm:text-xl font-bold text-gray-900 dark:text-white mb-2">Nenhum produto cadastrado</h3>
      <p class="text-sm sm:text-base text-gray-500 dark:text-gray-400 max-w-sm mx-auto">
        Adicione seu primeiro produto usando o formulário acima para começar a gerenciar seu estoque.
      </p>
      <div class="mt-6 flex justify-center">
        <div class="px-4 py-2 rounded-full bg-gradient-to-r from-indigo-500/10 to-purple-500/10 dark:from-indigo-500/20 dark:to-purple-500/20">
          <span class="text-sm font-medium text-indigo-600 dark:text-indigo-400">
            Dica: Use o formulário "Adicionar Produto" acima
          </span>
        </div>
      </div>
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
  transition: all 0.4s ease;
}

.list-enter-from {
  opacity: 0;
  transform: translateY(-15px) scale(0.98);
}

.list-leave-to {
  opacity: 0;
  transform: translateX(30px) scale(0.98);
}

.list-move {
  transition: transform 0.4s ease;
}
</style>
