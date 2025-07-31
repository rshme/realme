<template>
  <div class="min-h-screen bg-gradient-to-br from-purple-400 via-pink-500 to-red-500">
    <!-- Header Section -->
    <div class="safe-area-top pt-8 pb-16 px-6 text-center">
      <div class="mb-8">
        <div class="w-24 h-24 mx-auto mb-6 bg-white rounded-full flex items-center justify-center shadow-xl">
          <FeatherIcon name="heart" size="48" class="text-purple-600" />
        </div>
        <h1 class="text-4xl font-bold text-white mb-2">RealMe</h1>
        <p class="text-white/90 text-lg">Terima dirimu apa adanya</p>
      </div>
      
      <div class="bg-white/20 backdrop-blur-sm rounded-2xl p-6 mb-8">
        <p class="text-white text-center leading-relaxed">
          Aplikasi peer support untuk meningkatkan self-acceptance remaja Indonesia. 
          Mulai perjalanan mencintai diri sendiri bersama komunitas yang mendukung.
        </p>
      </div>
      
      <div class="space-y-4">
        <NuxtLink to="/auth/register" class="block w-full bg-white text-purple-600 font-semibold py-4 px-6 rounded-xl shadow-lg hover:shadow-xl transform hover:scale-105 transition-all duration-200">
          <FeatherIcon name="user-plus" size="20" class="inline mr-2" />
          Mulai Perjalanan
        </NuxtLink>
        
        <NuxtLink to="/auth/login" class="block w-full border-2 border-white text-white font-semibold py-4 px-6 rounded-xl hover:bg-white hover:text-purple-600 transition-all duration-200">
          <FeatherIcon name="log-in" size="20" class="inline mr-2" />
          Masuk Akun
        </NuxtLink>
      </div>
    </div>
    
    <!-- Features Preview -->
    <div class="bg-white rounded-t-3xl px-6 py-8">
      <h2 class="text-2xl font-bold text-gray-900 mb-6 text-center">Fitur Utama</h2>
      <div class="grid gap-4">
        <div class="flex items-center p-4 bg-purple-50 rounded-xl">
          <div class="w-12 h-12 bg-purple-100 rounded-full flex items-center justify-center mr-4">
            <FeatherIcon name="book-open" size="24" class="text-purple-600" />
          </div>
          <div>
            <h3 class="font-semibold text-gray-900">Daily Journaling</h3>
            <p class="text-gray-600 text-sm">Tulis perasaanmu setiap hari</p>
          </div>
        </div>
        
        <div class="flex items-center p-4 bg-pink-50 rounded-xl">
          <div class="w-12 h-12 bg-pink-100 rounded-full flex items-center justify-center mr-4">
            <FeatherIcon name="smile" size="24" class="text-pink-600" />
          </div>
          <div>
            <h3 class="font-semibold text-gray-900">Mood Tracking</h3>
            <p class="text-gray-600 text-sm">Pantau suasana hatimu</p>
          </div>
        </div>
        
        <div class="flex items-center p-4 bg-blue-50 rounded-xl">
          <div class="w-12 h-12 bg-blue-100 rounded-full flex items-center justify-center mr-4">
            <FeatherIcon name="users" size="24" class="text-blue-600" />
          </div>
          <div>
            <h3 class="font-semibold text-gray-900">Safe Community</h3>
            <p class="text-gray-600 text-sm">Berbagi dengan aman dan anonim</p>
          </div>
        </div>
        
        <div class="flex items-center p-4 bg-green-50 rounded-xl">
          <div class="w-12 h-12 bg-green-100 rounded-full flex items-center justify-center mr-4">
            <FeatherIcon name="award" size="24" class="text-green-600" />
          </div>
          <div>
            <h3 class="font-semibold text-gray-900">Daily Challenges</h3>
            <p class="text-gray-600 text-sm">Tantangan positif setiap hari</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
// Meta untuk halaman landing
useHead({
  title: 'RealMe - Aplikasi Peer Support untuk Self-Acceptance',
  meta: [
    { name: 'description', content: 'Aplikasi peer support untuk meningkatkan self-acceptance remaja Indonesia. Mulai perjalanan mencintai diri sendiri.' }
  ]
})
</script>
