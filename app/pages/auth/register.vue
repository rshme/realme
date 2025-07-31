<template>
  <div class="min-h-screen bg-gray-50">
    <!-- Header -->
    <div class="safe-area-top bg-white shadow-sm">
      <div class="flex items-center justify-between p-4">
        <button @click="$router.back()" class="p-2 hover:bg-gray-100 rounded-full">
          <FeatherIcon name="arrow-left" size="24" class="text-gray-600" />
        </button>
        <h1 class="text-xl font-semibold text-gray-900">Daftar Akun</h1>
        <div class="w-10"></div>
      </div>
    </div>
    
    <!-- Registration Form -->
    <div class="p-6">
      <div class="bg-white rounded-2xl shadow-sm border border-gray-100 p-6 mb-6">
        <div class="text-center mb-8">
          <div class="w-20 h-20 mx-auto mb-4 bg-purple-100 rounded-full flex items-center justify-center">
            <FeatherIcon name="user-plus" size="32" class="text-purple-600" />
          </div>
          <h2 class="text-2xl font-bold text-gray-900 mb-2">Bergabung dengan RealMe</h2>
          <p class="text-gray-600">Mulai perjalanan self-acceptance mu</p>
        </div>
        
        <form @submit.prevent="handleRegister" class="space-y-4">
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-2">Nama Lengkap</label>
            <input 
              v-model="form.name"
              type="text" 
              required
              class="input-field"
              placeholder="Masukkan nama lengkap"
            />
          </div>
          
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-2">Email</label>
            <input 
              v-model="form.email"
              type="email" 
              required
              class="input-field"
              placeholder="nama@email.com"
            />
          </div>
          
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-2">Usia</label>
            <input 
              v-model="form.age"
              type="number" 
              min="13"
              max="35"
              required
              class="input-field"
              placeholder="Masukkan usia"
            />
          </div>
          
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-2">Password</label>
            <div class="relative">
              <input 
                v-model="form.password"
                :type="showPassword ? 'text' : 'password'"
                required
                class="input-field pr-12"
                placeholder="Minimal 8 karakter"
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
          
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-2">Konfirmasi Password</label>
            <input 
              v-model="form.confirmPassword"
              type="password"
              required
              class="input-field"
              placeholder="Ulangi password"
            />
          </div>
          
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-2">Tujuan Menggunakan RealMe</label>
            <select v-model="form.goal" required class="input-field">
              <option value="">Pilih tujuan</option>
              <option value="self-acceptance">Meningkatkan self-acceptance</option>
              <option value="body-image">Memperbaiki body image</option>
              <option value="confidence">Membangun kepercayaan diri</option>
              <option value="stress-management">Mengelola stres</option>
              <option value="peer-support">Mencari dukungan teman sebaya</option>
            </select>
          </div>
          
          <div class="flex items-start space-x-3">
            <input 
              id="terms"
              v-model="form.agreeTerms"
              type="checkbox" 
              required
              class="mt-1 h-4 w-4 text-purple-600 focus:ring-purple-500 border-gray-300 rounded"
            />
            <label for="terms" class="text-sm text-gray-600">
              Saya setuju dengan 
              <NuxtLink to="/terms" class="text-purple-600 hover:underline">Syarat & Ketentuan</NuxtLink> 
              dan 
              <NuxtLink to="/privacy" class="text-purple-600 hover:underline">Kebijakan Privasi</NuxtLink>
            </label>
          </div>
          
          <button 
            type="submit"
            :disabled="loading"
            class="btn-primary w-full flex items-center justify-center"
          >
            <FeatherIcon v-if="loading" name="loader" size="20" class="mr-2 animate-spin" />
            <FeatherIcon v-else name="user-plus" size="20" class="mr-2" />
            {{ loading ? 'Mendaftar...' : 'Daftar Sekarang' }}
          </button>
        </form>
        
        <div class="mt-6 text-center">
          <p class="text-gray-600">Sudah punya akun? 
            <NuxtLink to="/auth/login" class="text-purple-600 font-medium hover:underline">
              Masuk di sini
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
              <span class="px-2 bg-white text-gray-500">Atau daftar dengan</span>
            </div>
          </div>
          
          <button 
            @click="handleGoogleSignup"
            class="mt-4 w-full flex items-center justify-center px-4 py-3 border border-gray-300 rounded-xl text-gray-700 bg-white hover:bg-gray-50 transition-colors duration-200"
          >
            <svg class="w-5 h-5 mr-2" viewBox="0 0 24 24">
              <path fill="#4285F4" d="M22.56 12.25c0-.78-.07-1.53-.2-2.25H12v4.26h5.92c-.26 1.37-1.04 2.53-2.21 3.31v2.77h3.57c2.08-1.92 3.28-4.74 3.28-8.09z"/>
              <path fill="#34A853" d="M12 23c2.97 0 5.46-.98 7.28-2.66l-3.57-2.77c-.98.66-2.23 1.06-3.71 1.06-2.86 0-5.29-1.93-6.16-4.53H2.18v2.84C3.99 20.53 7.7 23 12 23z"/>
              <path fill="#FBBC05" d="M5.84 14.09c-.22-.66-.35-1.36-.35-2.09s.13-1.43.35-2.09V7.07H2.18C1.43 8.55 1 10.22 1 12s.43 3.45 1.18 4.93l2.85-2.22.81-.62z"/>
              <path fill="#EA4335" d="M12 5.38c1.62 0 3.06.56 4.21 1.64l3.15-3.15C17.45 2.09 14.97 1 12 1 7.7 1 3.99 3.47 2.18 7.07l3.66 2.84c.87-2.6 3.3-4.53 6.16-4.53z"/>
            </svg>
            Daftar dengan Google
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive } from 'vue'

// Meta untuk halaman register
useHead({
  title: 'Daftar Akun - RealMe',
  meta: [
    { name: 'description', content: 'Daftar akun RealMe dan mulai perjalanan self-acceptance bersama komunitas yang mendukung.' }
  ]
})

// Form state
const form = reactive({
  name: '',
  email: '',
  age: '',
  password: '',
  confirmPassword: '',
  goal: '',
  agreeTerms: false
})

const showPassword = ref(false)
const loading = ref(false)

// Handle registration
const handleRegister = async () => {
  if (form.password !== form.confirmPassword) {
    alert('Password dan konfirmasi password tidak sama!')
    return
  }
  
  if (form.password.length < 8) {
    alert('Password minimal 8 karakter!')
    return
  }
  
  loading.value = true
  
  try {
    // Simulasi API call
    await new Promise(resolve => setTimeout(resolve, 2000))
    
    // Redirect ke onboarding setelah berhasil register
    await navigateTo('/onboarding')
  } catch (error) {
    console.error('Registration error:', error)
    alert('Terjadi kesalahan saat mendaftar. Silakan coba lagi.')
  } finally {
    loading.value = false
  }
}

// Handle Google signup
const handleGoogleSignup = async () => {
  try {
    // Simulasi Google OAuth
    await navigateTo('/onboarding')
  } catch (error) {
    console.error('Google signup error:', error)
    alert('Terjadi kesalahan saat mendaftar dengan Google.')
  }
}
</script>
