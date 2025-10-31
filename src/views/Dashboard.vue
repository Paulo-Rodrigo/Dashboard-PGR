<template>
  <div class="min-h-screen bg-gray-50">
    <header class="bg-white shadow-sm border-b border-gray-200">
      <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div class="flex justify-between items-center h-16">
          <div class="flex items-center">
            <div class="w-8 h-8 bg-primary-600 rounded-lg flex items-center justify-center mr-3">
              <svg class="w-5 h-5 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012 2v14a2 2 0 01-2 2h-2a2 2 0 01-2-2z"></path>
              </svg>
            </div>
            <h1 class="text-xl font-semibold text-gray-900">Dashboard PGR</h1>
          </div>
          
          <div class="flex items-center space-x-4">
            <div class="flex items-center space-x-2">
              <div class="w-2 h-2 bg-success-500 rounded-full animate-pulse"></div>
              <span class="text-sm text-gray-600">Sistema Online</span>
            </div>
            <span class="text-sm text-gray-600">Bem-vindo, {{ username }}</span>
            <button
              @click="handleLogout"
              class="text-gray-500 hover:text-gray-700 transition-colors duration-200"
              title="Sair"
            >
              <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 16l4-4m0 0l-4-4m4 4H7m6 4v1a3 3 0 01-3 3H6a3 3 0 01-3-3V7a3 3 0 013-3h4a3 3 0 013 3v1"></path>
              </svg>
            </button>
          </div>
        </div>
      </div>
    </header>

    <main class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
      <div class="mb-8">
        <h2 class="text-3xl font-bold text-gray-900 mb-2">{{ companyName }}</h2>
        <p class="text-gray-600">Sistema de Gestão de Programa de Gerenciamento de Riscos</p>
      </div>

      <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6 mb-8">
        <MetricCard
          title="Processos Concluídos"
          :value="58"
          :trend="12"
          :icon="CheckIcon"
          icon-class="text-white"
          icon-bg-class="bg-green-600"
          description="Aumento de 12% em relação ao mês anterior"
        />
        <MetricCard
          title="Em Andamento"
          :value="23"
          :trend="-5"
          :icon="ClockIcon"
          icon-class="text-white"
          icon-bg-class="bg-yellow-500"
          description="Redução de 5% comparado ao mês passado"
        />
        <MetricCard
          title="Pendentes"
          :value="7"
          :trend="-15"
          :icon="AlertIcon"
          icon-class="text-white"
          icon-bg-class="bg-red-600"
          description="Redução significativa de 15%"
        />
        <MetricCard
          title="Taxa de Conformidade"
          :value="94.2"
          :trend="3"
          :icon="TrendIcon"
          icon-class="text-white"
          icon-bg-class="bg-blue-600"
          description="Excelente performance de conformidade"
        />
      </div>

      <div class="grid grid-cols-1 lg:grid-cols-2 gap-6 mb-8">
        <ApexChart
          title="Status dos Processos"
          type="bar"
          :data="statusData"
          :show-legend="true"
          :legend-items="[{ label: 'Tempo Real', color: '#22c55e' }]"
        />
        <ApexChart
          title="Tendência Mensal"
          type="line"
          :data="trendData"
          :show-legend="true"
          :legend-items="[
            { label: 'Processos', color: '#3b82f6' },
            { label: 'Concluídos', color: '#22c55e' }
          ]"
        />
      </div>

      <div class="grid grid-cols-1 lg:grid-cols-3 gap-6 mb-8">
        <ApexChart
          title="Distribuição por Departamento"
          type="donut"
          :data="pieData"
        />
        <div class="lg:col-span-2">
          <div class="card">
            <div class="flex items-center justify-between mb-4">
              <h3 class="text-lg font-semibold text-gray-900">Status do PGR</h3>
              <button
                @click="cycleStatus"
                class="text-gray-400 hover:text-gray-600 transition-colors duration-200"
                title="Alterar status"
              >
                <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"></path>
                </svg>
              </button>
            </div>
            
            <div class="flex items-center space-x-3 mb-4">
              <div :class="statusClasses" class="w-4 h-4 rounded-full"></div>
              <span :class="statusTextClasses" class="font-medium text-lg">{{ currentStatus.label }}</span>
            </div>
            
            <p class="text-gray-600 mb-4">{{ currentStatus.description }}</p>
            
            <div class="space-y-3">
              <button
                @click="exportReport"
                :disabled="exporting"
                class="w-full flex items-center justify-center space-x-2 btn-primary disabled:opacity-50 disabled:cursor-not-allowed"
              >
                <svg v-if="!exporting" class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 10v6m0 0l-3-3m3 3l3-3m2 8H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z"></path>
                </svg>
                <svg v-else class="animate-spin w-5 h-5" fill="none" viewBox="0 0 24 24">
                  <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                  <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
                </svg>
                <span>{{ exporting ? 'Exportando...' : 'Exportar Relatório' }}</span>
              </button>
              
              <button
                @click="refreshData"
                :disabled="refreshing"
                class="w-full flex items-center justify-center space-x-2 btn-secondary disabled:opacity-50 disabled:cursor-not-allowed"
              >
                <svg v-if="!refreshing" class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"></path>
                </svg>
                <svg v-else class="animate-spin w-5 h-5" fill="none" viewBox="0 0 24 24">
                  <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                  <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
                </svg>
                <span>{{ refreshing ? 'Atualizando...' : 'Atualizar Dados' }}</span>
              </button>
            </div>
            
            <div class="mt-4 pt-4 border-t border-gray-200">
              <div class="flex items-center justify-between text-sm text-gray-500">
                <span>Última atualização:</span>
                <span>{{ lastUpdate }}</span>
              </div>
            </div>
          </div>
      </div>
    </div>
  </main>

  <!-- Chatbot FAB -->
  <div class="fixed bottom-6 right-6 z-50">
    <!-- Chat Window -->
    <div v-if="chatOpen" class="mb-4 bg-white rounded-lg shadow-2xl border border-gray-200 w-80 h-96 flex flex-col">
      <!-- Chat Header -->
      <div class="bg-primary-600 text-white p-4 rounded-t-lg flex items-center justify-between">
        <div class="flex items-center space-x-3">
          <div class="w-8 h-8 bg-white bg-opacity-20 rounded-full flex items-center justify-center">
            <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 24 24">
              <path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm-2 15l-5-5 1.41-1.41L10 14.17l7.59-7.59L19 8l-9 9z"/>
            </svg>
          </div>
          <div>
            <h3 class="font-semibold">Assistente PGR</h3>
            <p class="text-xs opacity-90">Online</p>
          </div>
        </div>
        <button @click="toggleChat" class="text-white hover:text-gray-200 transition-colors">
          <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
          </svg>
        </button>
      </div>
      
      <!-- Chat Messages -->
      <div class="flex-1 p-4 overflow-y-auto space-y-3">
        <div class="flex items-start space-x-2">
          <div class="w-6 h-6 bg-primary-100 rounded-full flex items-center justify-center flex-shrink-0">
            <svg class="w-4 h-4 text-primary-600" fill="currentColor" viewBox="0 0 24 24">
              <path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm-2 15l-5-5 1.41-1.41L10 14.17l7.59-7.59L19 8l-9 9z"/>
            </svg>
          </div>
          <div class="bg-gray-100 rounded-lg p-3 max-w-xs">
            <p class="text-sm text-gray-800">Olá! Como posso ajudá-lo com o PGR hoje?</p>
          </div>
        </div>
        
        <div class="flex items-start space-x-2 justify-end">
          <div class="bg-primary-600 text-white rounded-lg p-3 max-w-xs">
            <p class="text-sm">Preciso de ajuda com os relatórios</p>
          </div>
          <div class="w-6 h-6 bg-gray-300 rounded-full flex items-center justify-center flex-shrink-0">
            <svg class="w-4 h-4 text-gray-600" fill="currentColor" viewBox="0 0 24 24">
              <path d="M12 12c2.21 0 4-1.79 4-4s-1.79-4-4-4-4 1.79-4 4 1.79 4 4 4zm0 2c-2.67 0-8 1.34-8 4v2h16v-2c0-2.66-5.33-4-8-4z"/>
            </svg>
          </div>
        </div>
        
        <div class="flex items-start space-x-2">
          <div class="w-6 h-6 bg-primary-100 rounded-full flex items-center justify-center flex-shrink-0">
            <svg class="w-4 h-4 text-primary-600" fill="currentColor" viewBox="0 0 24 24">
              <path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm-2 15l-5-5 1.41-1.41L10 14.17l7.59-7.59L19 8l-9 9z"/>
            </svg>
          </div>
          <div class="bg-gray-100 rounded-lg p-3 max-w-xs">
            <p class="text-sm text-gray-800">Posso ajudá-lo a gerar relatórios executivos e explicar as métricas do dashboard.</p>
          </div>
        </div>
        
        <div class="flex items-start space-x-2">
          <div class="w-6 h-6 bg-primary-100 rounded-full flex items-center justify-center flex-shrink-0">
            <svg class="w-4 h-4 text-primary-600" fill="currentColor" viewBox="0 0 24 24">
              <path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm-2 15l-5-5 1.41-1.41L10 14.17l7.59-7.59L19 8l-9 9z"/>
            </svg>
          </div>
          <div class="bg-gray-100 rounded-lg p-3 max-w-xs">
            <p class="text-sm text-gray-800">Digite sua pergunta abaixo!</p>
          </div>
        </div>
      </div>
      
      <!-- Chat Input -->
      <div class="p-4 border-t border-gray-200">
        <div class="flex space-x-2">
          <input 
            type="text" 
            placeholder="Digite sua mensagem..."
            class="flex-1 px-3 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-primary-500 focus:border-transparent text-sm"
            @keyup.enter="sendMessage"
            v-model="messageInput"
          >
          <button 
            @click="sendMessage"
            class="bg-primary-600 text-white px-4 py-2 rounded-lg hover:bg-primary-700 transition-colors"
          >
            <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 19l9 2-9-18-9 18 9-2zm0 0v-8"></path>
            </svg>
          </button>
        </div>
      </div>
    </div>
    
    <!-- FAB Button -->
    <button 
      @click="toggleChat"
      class="w-14 h-14 bg-primary-600 hover:bg-primary-700 text-white rounded-full shadow-lg hover:shadow-xl transition-all duration-300 flex items-center justify-center group"
      :class="{ 'rotate-45': chatOpen }"
    >
      <svg v-if="!chatOpen" class="w-6 h-6 transition-transform duration-300" fill="none" stroke="currentColor" viewBox="0 0 24 24">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 12h.01M12 12h.01M16 12h.01M21 12c0 4.418-4.03 8-9 8a9.863 9.863 0 01-4.255-.949L3 20l1.395-3.72C3.512 15.042 3 13.574 3 12c0-4.418 4.03-8 9-8s9 3.582 9 8z"></path>
      </svg>
      <svg v-else class="w-6 h-6 transition-transform duration-300" fill="none" stroke="currentColor" viewBox="0 0 24 24">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
      </svg>
    </button>
  </div>
</div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import ApexChart from '../components/ApexChart.vue'
import MetricCard from '../components/MetricCard.vue'

const CheckIcon = 'svg'
const ClockIcon = 'svg'
const AlertIcon = 'svg'
const TrendIcon = 'svg'

const router = useRouter()

const username = ref('')
const companyName = 'TechCorp Solutions Ltda.'
const exporting = ref(false)
const refreshing = ref(false)
const chatOpen = ref(false)
const messageInput = ref('')

const statusData = {
  labels: ['Concluídos', 'Em Andamento', 'Pendentes', 'Em Revisão', 'Aprovados'],
  datasets: [{
    data: [24, 8, 3, 5, 18],
    backgroundColor: [
      'rgba(34, 197, 94, 0.8)',
      'rgba(245, 158, 11, 0.8)',
      'rgba(239, 68, 68, 0.8)',
      'rgba(59, 130, 246, 0.8)',
      'rgba(16, 185, 129, 0.8)'
    ]
  }]
}

const trendData = {
  labels: ['Jan', 'Fev', 'Mar', 'Abr', 'Mai', 'Jun', 'Jul', 'Ago', 'Set', 'Out', 'Nov', 'Dez'],
  datasets: [
    {
      label: 'Processos Criados',
      data: [12, 19, 15, 25, 22, 18, 30, 28, 35, 32, 28, 24],
      borderColor: 'rgb(59, 130, 246)',
      backgroundColor: 'rgba(59, 130, 246, 0.1)'
    },
    {
      label: 'Processos Concluídos',
      data: [8, 15, 12, 20, 18, 16, 25, 24, 30, 28, 25, 22],
      borderColor: 'rgb(34, 197, 94)',
      backgroundColor: 'rgba(34, 197, 94, 0.1)'
    }
  ]
}

const pieData = {
  labels: ['RH', 'TI', 'Financeiro', 'Operações', 'Marketing', 'Jurídico'],
  datasets: [{
    data: [25, 20, 18, 15, 12, 10],
    backgroundColor: [
      '#3b82f6',
      '#10b981',
      '#f59e0b',
      '#ef4444',
      '#8b5cf6',
      '#06b6d4'
    ]
  }]
}

const statusIndex = ref(0)
const statusOptions = [
  {
    label: 'Ativo',
    description: 'Todos os processos estão em conformidade e funcionando adequadamente.',
    color: 'success'
  },
  {
    label: 'Em Andamento',
    description: 'Alguns processos estão sendo atualizados e revisados.',
    color: 'warning'
  },
  {
    label: 'Pendente',
    description: 'Atenção necessária: existem processos que requerem ação imediata.',
    color: 'danger'
  }
]

const currentStatus = computed(() => statusOptions[statusIndex.value])

const statusClasses = computed(() => {
  const color = currentStatus.value.color
  return {
    'bg-success-500': color === 'success',
    'bg-warning-500': color === 'warning',
    'bg-danger-500': color === 'danger'
  }
})

const statusTextClasses = computed(() => {
  const color = currentStatus.value.color
  return {
    'text-success-600': color === 'success',
    'text-warning-600': color === 'warning',
    'text-danger-600': color === 'danger'
  }
})

const lastUpdate = ref('')

const cycleStatus = () => {
  statusIndex.value = (statusIndex.value + 1) % statusOptions.length
  updateLastUpdate()
}

const updateLastUpdate = () => {
  const now = new Date()
  lastUpdate.value = now.toLocaleString('pt-BR')
}

const exportReport = async () => {
  exporting.value = true
  
  await new Promise(resolve => setTimeout(resolve, 2000))
  
  const htmlContent = `
    <!DOCTYPE html>
    <html>
    <head>
      <meta charset="UTF-8">
      <title>Relatório PGR - TechCorp Solutions</title>
      <style>
        body { font-family: Arial, sans-serif; margin: 40px; color: #333; line-height: 1.6; }
        .header { text-align: center; margin-bottom: 40px; border-bottom: 2px solid #2563eb; padding-bottom: 20px; }
        .company { font-size: 28px; font-weight: bold; color: #2563eb; margin-bottom: 10px; }
        .title { font-size: 22px; margin: 20px 0; color: #374151; }
        .subtitle { font-size: 16px; color: #6b7280; }
        .section { margin: 30px 0; padding: 20px; border: 1px solid #e5e7eb; border-radius: 8px; }
        .section-title { font-size: 18px; font-weight: bold; color: #1f2937; margin-bottom: 15px; }
        .metrics-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 20px; margin: 20px 0; }
        .metric-card { padding: 15px; border-radius: 8px; border-left: 4px solid; }
        .metric-card.success { background-color: #f0fdf4; border-left-color: #22c55e; }
        .metric-card.warning { background-color: #fffbeb; border-left-color: #f59e0b; }
        .metric-card.danger { background-color: #fef2f2; border-left-color: #ef4444; }
        .metric-card.primary { background-color: #eff6ff; border-left-color: #3b82f6; }
        .metric-title { font-size: 14px; color: #6b7280; margin-bottom: 5px; }
        .metric-value { font-size: 24px; font-weight: bold; color: #1f2937; }
        .metric-trend { font-size: 12px; margin-top: 5px; }
        .trend-up { color: #16a34a; }
        .trend-down { color: #dc2626; }
        .status { padding: 15px; border-radius: 8px; margin: 20px 0; text-align: center; }
        .status-ativo { background-color: #dcfce7; color: #16a34a; border: 2px solid #22c55e; }
        .status-andamento { background-color: #fef3c7; color: #d97706; border: 2px solid #f59e0b; }
        .status-pendente { background-color: #fee2e2; color: #dc2626; border: 2px solid #ef4444; }
        .chart-data { margin: 20px 0; }
        .chart-title { font-size: 16px; font-weight: bold; margin-bottom: 10px; color: #374151; }
        .data-table { width: 100%; border-collapse: collapse; margin: 15px 0; }
        .data-table th, .data-table td { padding: 10px; text-align: left; border-bottom: 1px solid #e5e7eb; }
        .data-table th { background-color: #f9fafb; font-weight: bold; color: #374151; }
        .info { margin: 15px 0; }
        .footer { margin-top: 50px; text-align: center; color: #666; font-size: 12px; border-top: 1px solid #e5e7eb; padding-top: 20px; }
        @media print { body { margin: 20px; } .section { break-inside: avoid; } }
      </style>
    </head>
    <body>
      <div class="header">
        <div class="company">TechCorp Solutions Ltda.</div>
        <div class="title">Relatório Executivo - Dashboard PGR</div>
        <div class="subtitle">Sistema de Gestão de Programa de Gerenciamento de Riscos</div>
      </div>
      
      <div class="info">
        <strong>Data de Geração:</strong> ${new Date().toLocaleString('pt-BR')}
      </div>
      
      <div class="info">
        <strong>Última Atualização:</strong> ${lastUpdate.value}
      </div>
      
      <div class="section">
        <div class="section-title">📊 Métricas Principais</div>
        <div class="metrics-grid">
          <div class="metric-card success">
            <div class="metric-title">Processos Concluídos</div>
            <div class="metric-value">58</div>
            <div class="metric-trend trend-up">↗ +12% vs mês anterior</div>
          </div>
          <div class="metric-card warning">
            <div class="metric-title">Em Andamento</div>
            <div class="metric-value">23</div>
            <div class="metric-trend trend-down">↘ -5% vs mês anterior</div>
          </div>
          <div class="metric-card danger">
            <div class="metric-title">Pendentes</div>
            <div class="metric-value">7</div>
            <div class="metric-trend trend-down">↘ -15% vs mês anterior</div>
          </div>
          <div class="metric-card primary">
            <div class="metric-title">Taxa de Conformidade</div>
            <div class="metric-value">94.2%</div>
            <div class="metric-trend trend-up">↗ +3% vs mês anterior</div>
          </div>
        </div>
      </div>
      
      <div class="section">
        <div class="section-title">🎯 Status do PGR</div>
        <div class="status status-${currentStatus.value.color === 'success' ? 'ativo' : currentStatus.value.color === 'warning' ? 'andamento' : 'pendente'}">
          <strong>${currentStatus.value.label}</strong><br>
          ${currentStatus.value.description}
        </div>
      </div>
      
      <div class="section">
        <div class="section-title">📈 Status dos Processos</div>
        <div class="chart-data">
          <div class="chart-title">Distribuição por Categoria</div>
          <table class="data-table">
            <thead>
              <tr>
                <th>Categoria</th>
                <th>Quantidade</th>
                <th>Percentual</th>
              </tr>
            </thead>
            <tbody>
              ${statusData.labels.map((label, index) => `
                <tr>
                  <td>${label}</td>
                  <td>${statusData.datasets[0].data[index]}</td>
                  <td>${((statusData.datasets[0].data[index] / statusData.datasets[0].data.reduce((a, b) => a + b, 0)) * 100).toFixed(1)}%</td>
                </tr>
              `).join('')}
            </tbody>
          </table>
        </div>
      </div>
      
      <div class="section">
        <div class="section-title">📊 Tendência Mensal</div>
        <div class="chart-data">
          <div class="chart-title">Evolução dos Últimos 12 Meses</div>
          <table class="data-table">
            <thead>
              <tr>
                <th>Mês</th>
                <th>Processos Criados</th>
                <th>Processos Concluídos</th>
                <th>Taxa de Conclusão</th>
              </tr>
            </thead>
            <tbody>
              ${trendData.labels.map((month, index) => {
                const criados = trendData.datasets[0].data[index]
                const concluidos = trendData.datasets[1].data[index]
                const taxa = ((concluidos / criados) * 100).toFixed(1)
                return `
                  <tr>
                    <td>${month}</td>
                    <td>${criados}</td>
                    <td>${concluidos}</td>
                    <td>${taxa}%</td>
                  </tr>
                `
              }).join('')}
            </tbody>
          </table>
        </div>
      </div>
      
      <div class="section">
        <div class="section-title">🏢 Distribuição por Departamento</div>
        <div class="chart-data">
          <div class="chart-title">Participação por Área</div>
          <table class="data-table">
            <thead>
              <tr>
                <th>Departamento</th>
                <th>Percentual</th>
                <th>Status</th>
              </tr>
            </thead>
            <tbody>
              ${pieData.labels.map((dept, index) => {
                const percent = pieData.datasets[0].data[index]
                const status = percent >= 20 ? 'Excelente' : percent >= 15 ? 'Bom' : percent >= 10 ? 'Regular' : 'Atenção'
                return `
                  <tr>
                    <td>${dept}</td>
                    <td>${percent}%</td>
                    <td>${status}</td>
                  </tr>
                `
              }).join('')}
            </tbody>
          </table>
        </div>
      </div>
      
      <div class="section">
        <div class="section-title">📋 Resumo Executivo</div>
        <div class="info">
          <strong>Principais Conquistas:</strong>
          <ul>
            <li>Taxa de conformidade de 94.2%, acima da meta de 90%</li>
            <li>Redução de 15% nos processos pendentes</li>
            <li>Aumento de 12% nos processos concluídos</li>
            <li>Status geral do PGR: ${currentStatus.value.label}</li>
          </ul>
        </div>
        <div class="info">
          <strong>Recomendações:</strong>
          <ul>
            <li>Manter foco na redução de processos pendentes</li>
            <li>Continuar monitoramento da taxa de conformidade</li>
            <li>Revisar processos dos departamentos com menor participação</li>
            <li>Implementar melhorias baseadas nas tendências mensais</li>
          </ul>
        </div>
      </div>
      
      <div class="footer">
        <p><strong>Relatório gerado automaticamente pelo Sistema de Gestão PGR</strong></p>
        <p>TechCorp Solutions Ltda. - Todos os direitos reservados</p>
        <p>Este relatório contém informações confidenciais e é destinado exclusivamente ao uso interno da empresa.</p>
      </div>
    </body>
    </html>
  `
  
  const blob = new Blob([htmlContent], { type: 'text/html' })
  const url = URL.createObjectURL(blob)
  
  const newWindow = window.open(url, '_blank')
  if (newWindow) {
    newWindow.onload = () => {
      newWindow.print()
    }
  }
  
  const a = document.createElement('a')
  a.href = url
  a.download = `relatorio-pgr-executivo-${new Date().toISOString().split('T')[0]}.html`
  document.body.appendChild(a)
  a.click()
  document.body.removeChild(a)
  
  setTimeout(() => {
    URL.revokeObjectURL(url)
  }, 1000)
  
  exporting.value = false
}

const refreshData = async () => {
  refreshing.value = true
  await new Promise(resolve => setTimeout(resolve, 1000))
  updateLastUpdate()
  refreshing.value = false
}

const toggleChat = () => {
  chatOpen.value = !chatOpen.value
}

const sendMessage = () => {
  if (messageInput.value.trim()) {
    console.log('Mensagem enviada:', messageInput.value)
    messageInput.value = ''
  }
}

const handleLogout = () => {
  localStorage.removeItem('isAuthenticated')
  localStorage.removeItem('username')
  router.push('/login')
}

onMounted(() => {
  username.value = localStorage.getItem('username') || 'Usuário'
  updateLastUpdate()
})
</script>
