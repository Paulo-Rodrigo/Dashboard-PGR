<template>
  <div class="card hover:shadow-lg transition-shadow duration-300">
    <div class="flex items-center justify-between">
      <div class="flex items-center">
        <div :class="iconBgClass" class="w-12 h-12 rounded-lg flex items-center justify-center">
          <component :is="icon" class="w-6 h-6" :class="iconClass" />
        </div>
        <div class="ml-4">
          <p class="text-sm font-medium text-gray-600">{{ title }}</p>
          <p class="text-2xl font-semibold text-gray-900">{{ value }}</p>
        </div>
      </div>
      <div v-if="trend" class="text-right">
        <div :class="trendClass" class="flex items-center text-sm font-medium">
          <svg v-if="trend > 0" class="w-4 h-4 mr-1" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 7h8m0 0v8m0-8l-8 8-4-4-6 6"></path>
          </svg>
          <svg v-else-if="trend < 0" class="w-4 h-4 mr-1" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 17h8m0 0V9m0 8l-8-8-4 4-6-6"></path>
          </svg>
          <svg v-else class="w-4 h-4 mr-1" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M20 12H4"></path>
          </svg>
          {{ Math.abs(trend) }}%
        </div>
        <p class="text-xs text-gray-500">vs mês anterior</p>
      </div>
    </div>
    <div v-if="description" class="mt-4 pt-4 border-t border-gray-200">
      <p class="text-sm text-gray-600">{{ description }}</p>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue'

interface Props {
  title: string
  value: string | number
  icon: any
  iconClass?: string
  iconBgClass?: string
  trend?: number
  description?: string
}

const props = withDefaults(defineProps<Props>(), {
  iconClass: 'text-white',
  iconBgClass: 'bg-primary-100',
  trend: 0
})

const trendClass = computed(() => {
  if (props.trend > 0) return 'text-success-600'
  if (props.trend < 0) return 'text-danger-600'
  return 'text-gray-600'
})
</script>
