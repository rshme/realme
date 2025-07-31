<template>
  <div class="min-h-screen bg-gray-50">
    <!-- Header -->
    <div class="safe-area-top bg-white shadow-sm">
      <div class="flex items-center justify-between p-4">
        <button @click="$router.back()" class="p-2 hover:bg-gray-100 rounded-full">
          <FeatherIcon name="arrow-left" size="24" class="text-gray-600" />
        </button>
        <h1 class="text-xl font-semibold text-gray-900">Masuk Akun</h1>
        <div class="w-10"></div>
      </div>
    </div>
    
    <!-- Login Form -->
    <div class="p-6">
      <div class="bg-white rounded-2xl shadow-sm border border-gray-100 p-6 mb-6">
        <div class="text-center mb-8">
          <div class="w-20 h-20 mx-auto mb-4 bg-purple-100 rounded-full flex items-center justify-center">
            <FeatherIcon name="log-in" size="32" class="text-purple-600" />
          </div>
          <h2 class="text-2xl font-bold text-gray-900 mb-2">Selamat Datang Kembali</h2>
          <p class="text-gray-600">Masuk dan lanjutkan perjalanan self-acceptance mu</p>
        </div>
        
        <form @submit.prevent="handleLogin" class="space-y-4">
          <div>
            <label for="email" class="block text-sm font-medium text-gray-700 mb-2">Email</label>
            <input 
              id="email"
              v-model="form.email"
              type="email" 
              required
              class="input-field"
              placeholder="nama@email.com"
            />
          </div>
          
          <div>
            <label for="password" class="block text-sm font-medium text-gray-700 mb-2">Password</label>
            <div class="relative">
              <input 
                id="password"
                v-model="form.password"
                :type="showPassword ? 'text' : 'password'"
                required
                class="input-field pr-12"
                placeholder="Masukkan password"
              />
              <button 
                type="button"
                @click="showPassword = !showPassword"
                class="absolute right-3 top-3 text-gray-500 hover:text-gray-700"
              >
                <FeatherIcon :name="showPassword ? 'eye-off' : 'eye'" size="20" />
              </button>
            </div>
          </div>
          
          <div class="flex items-center justify-between">
            <div class="flex items-center">
              <input 
                id="remember"
                v-model="form.rememberMe"
                type="checkbox" 
                class="h-4 w-4 text-purple-600 focus:ring-purple-500 border-gray-300 rounded"
              />
              <label for="remember" class="ml-2 block text-sm text-gray-700">
                Ingat saya
              </label>
            </div>
            
            <NuxtLink to="/auth/forgot-password" class="text-sm text-purple-600 hover:underline">
              Lupa password?
            </NuxtLink>
          </div>
          
          <button 
            type="submit"
            :disabled="loading"
            class="btn-primary w-full flex items-center justify-center"
          >
            <FeatherIcon v-if="loading" name="loader" size="20" class="mr-2 animate-spin" />
            <FeatherIcon v-else name="log-in" size="20" class="mr-2" />
            {{ loading ? 'Masuk...' : 'Masuk Akun' }}
          </button>
        </form>
        
        <div class="mt-6 text-center">
          <p class="text-gray-600">Belum punya akun? 
            <NuxtLink to="/auth/register" class="text-purple-600 font-medium hover:underline">
              Daftar di sini
            </NuxtLink>
          </p>
        </div>
        
        <!-- Social Login -->
        <div class="mt-6">
          <div class="relative">
            <div class="absolute inset-0 flex items-center">
              <div class="w-full border-t border-gray-300"></div>
            </div>
            <div class="relative flex justify-center text-sm">
              <span class="px-2 bg-white text-gray-500">Atau masuk dengan</span>
            </div>
          </div>
          
          <button 
            @click="handleGoogleLogin"
            class="mt-4 w-full flex items-center justify-center px-4 py-3 border border-gray-300 rounded-xl text-gray-700 bg-white hover:bg-gray-50 transition-colors duration-200"
          >
            <svg class="w-5 h-5 mr-2" viewBox="0 0 24 24">
              <path fill="#4285F4" d="M22.56 12.25c0-.78-.07-1.53-.2-2.25H12v4.26h5.92c-.26 1.37-1.04 2.53-2.21 3.31v2.77h3.57c2.08-1.92 3.28-4.74 3.28-8.09z"/>
              <path fill="#34A853" d="M12 23c2.97 0 5.46-.98 7.28-2.66l-3.57-2.77c-.98.66-2.23 1.06-3.71 1.06-2.86 0-5.29-1.93-6.16-4.53H2.18v2.84C3.99 20.53 7.7 23 12 23z"/>
              <path fill="#FBBC05" d="M5.84 14.09c-.22-.66-.35-1.36-.35-2.09s.13-1.43.35-2.09V7.07H2.18C1.43 8.55 1 10.22 1 12s.43 3.45 1.18 4.93l2.85-2.22.81-.62z"/>
              <path fill="#EA4335" d="M12 5.38c1.62 0 3.06.56 4.21 1.64l3.15-3.15C17.45 2.09 14.97 1 12 1 7.7 1 3.99 3.47 2.18 7.07l3.66 2.84c.87-2.6 3.3-4.53 6.16-4.53z"/>
            </svg>
            Masuk dengan Google
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive } from 'vue'

// Meta untuk halaman login
useHead({
  title: 'Masuk Akun - RealMe',
  meta: [
    { name: 'description', content: 'Masuk ke akun RealMe dan lanjutkan perjalanan self-acceptance bersama komunitas yang mendukung.' }
  ]
})

// Form state
const form = reactive({
  email: '',
  password: '',
  rememberMe: false
})

const showPassword = ref(false)
const loading = ref(false)

// Handle login
const handleLogin = async () => {
  loading.value = true
  
  try {
    // Simulasi API call
    await new Promise(resolve => setTimeout(resolve, 2000))
    
    // Redirect ke dashboard setelah berhasil login
    await navigateTo('/dashboard')
  } catch (error) {
    console.error('Login error:', error)
    alert('Email atau password salah. Silakan coba lagi.')
  } finally {
    loading.value = false
  }
}

// Handle Google login
const handleGoogleLogin = async () => {
  try {
    // Simulasi Google OAuth
    await navigateTo('/dashboard')
  } catch (error) {
    console.error('Google login error:', error)
    alert('Terjadi kesalahan saat masuk dengan Google.')
  }
}
</script>
