<template>
  <div class="min-h-screen flex items-center justify-center bg-gradient-to-br from-primary-50 to-primary-100 px-4">
    <div class="max-w-md w-full">
      <div class="card">
        <div class="text-center mb-8">
          <div class="w-16 h-16 bg-primary-600 rounded-full flex items-center justify-center mx-auto mb-4">
            <svg class="w-8 h-8 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z"></path>
            </svg>
          </div>
          <h1 class="text-2xl font-bold text-gray-900">Dashboard PGR</h1>
          <p class="text-gray-600 mt-2">Faça login para acessar o painel</p>
        </div>

        <form @submit.prevent="handleLogin" class="space-y-6">
          <div>
            <label for="username" class="block text-sm font-medium text-gray-700 mb-2">
              Usuário
            </label>
            <input
              id="username"
              v-model="credentials.username"
              type="text"
              class="input-field"
              placeholder="Digite seu usuário"
              required
            />
          </div>

          <div>
            <label for="password" class="block text-sm font-medium text-gray-700 mb-2">
              Senha
            </label>
            <input
              id="password"
              v-model="credentials.password"
              type="password"
              class="input-field"
              placeholder="Digite sua senha"
              required
            />
          </div>

          <div v-if="error" class="bg-danger-50 border border-danger-200 text-danger-600 px-4 py-3 rounded-lg">
            {{ error }}
          </div>

          <button
            type="submit"
            :disabled="loading"
            class="w-full btn-primary disabled:opacity-50 disabled:cursor-not-allowed"
          >
            <span v-if="loading" class="flex items-center justify-center">
              <svg class="animate-spin -ml-1 mr-3 h-5 w-5 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
                <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
              </svg>
              Entrando...
            </span>
            <span v-else>Entrar</span>
          </button>
        </form>

        <div class="mt-6 text-center text-sm text-gray-600">
          <p class="font-medium">Credenciais de teste:</p>
          <p>Usuário: <span class="font-mono bg-gray-100 px-2 py-1 rounded">admin</span></p>
          <p>Senha: <span class="font-mono bg-gray-100 px-2 py-1 rounded">123456</span></p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, reactive } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()

const credentials = reactive({
  username: '',
  password: ''
})

const loading = ref(false)
const error = ref('')

const handleLogin = async () => {
  loading.value = true
  error.value = ''

  await new Promise(resolve => setTimeout(resolve, 1000))

  if (credentials.username === 'admin' && credentials.password === '123456') {
    localStorage.setItem('isAuthenticated', 'true')
    localStorage.setItem('username', credentials.username)
    router.push('/dashboard')
  } else {
    error.value = 'Usuário ou senha incorretos'
  }

  loading.value = false
}
</script>
