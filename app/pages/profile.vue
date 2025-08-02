<template>
  <div class="min-h-screen bg-gray-50">
    <!-- Header -->
    <div class="safe-area-top bg-white shadow-sm">
      <div class="flex items-center justify-between p-4">
        <button @click="$router.back()" class="p-2 hover:bg-gray-100 rounded-full">
          <ArrowLeftIcon size="24" class="text-gray-600" />
        </button>
        <h1 class="text-xl font-semibold text-gray-900">Profil</h1>
        <button @click="showSettings = true" class="p-2 hover:bg-gray-100 rounded-full">
          <SettingsIcon size="24" class="text-gray-600" />
        </button>
      </div>
    </div>
    
    <!-- Profile Header -->
    <div class="px-6 py-6">
      <div class="bg-gradient-to-br from-purple-500 to-pink-600 rounded-2xl p-6 text-white">
        <div class="flex items-center">
          <div class="w-20 h-20 bg-white/20 backdrop-blur-sm rounded-full flex items-center justify-center mr-4">
            <span class="text-2xl font-bold text-white">{{ userInitials }}</span>
          </div>
          <div class="flex-1">
            <h2 class="text-xl font-bold">{{ userProfile.name }}</h2>
            <p class="text-white/80 mb-2">{{ userProfile.email }}</p>
            <div class="flex items-center">
              <CalendarIcon size="16" class="mr-2" />
              <span class="text-sm">Bergabung {{ userProfile.joinDate }}</span>
            </div>
          </div>
        </div>
      </div>
    </div>
    
    <!-- Stats Cards -->
    <div class="px-6 mb-6">
      <div class="grid grid-cols-2 gap-4">
        <div class="bg-white rounded-xl p-4 text-center shadow-sm border border-gray-100">
          <div class="w-12 h-12 bg-blue-100 rounded-full flex items-center justify-center mx-auto mb-2">
            <Edit3Icon size="24" class="text-blue-600" />
          </div>
          <div class="text-2xl font-bold text-gray-900">{{ userStats.journalDays }}</div>
          <div class="text-sm text-gray-600">Hari Journal</div>
        </div>
        
        <div class="bg-white rounded-xl p-4 text-center shadow-sm border border-gray-100">
          <div class="w-12 h-12 bg-green-100 rounded-full flex items-center justify-center mx-auto mb-2">
            <BookIcon size="24" class="text-green-600" />
          </div>
          <div class="text-2xl font-bold text-gray-900">{{ userStats.completedModules }}</div>
          <div class="text-sm text-gray-600">Modul Selesai</div>
        </div>
        
        <div class="bg-white rounded-xl p-4 text-center shadow-sm border border-gray-100">
          <div class="w-12 h-12 bg-purple-100 rounded-full flex items-center justify-center mx-auto mb-2">
            <ZapIcon size="24" class="text-purple-600" />
          </div>
          <div class="text-2xl font-bold text-gray-900">{{ userStats.totalXP }}</div>
          <div class="text-sm text-gray-600">Total XP</div>
        </div>
        
        <div class="bg-white rounded-xl p-4 text-center shadow-sm border border-gray-100">
          <div class="w-12 h-12 bg-yellow-100 rounded-full flex items-center justify-center mx-auto mb-2">
            <AwardIcon size="24" class="text-yellow-600" />
          </div>
          <div class="text-2xl font-bold text-gray-900">Level {{ currentLevel }}</div>
          <div class="text-sm text-gray-600">Progress</div>
        </div>
      </div>
    </div>
    
    <!-- Achievements -->
    <div class="px-6 mb-6">
      <h3 class="text-lg font-semibold text-gray-900 mb-3">Pencapaian Terbaru</h3>
      <div class="bg-white rounded-2xl p-4 shadow-sm border border-gray-100">
        <div class="space-y-3">
          <div 
            v-for="achievement in recentAchievements" 
            :key="achievement.id"
            class="flex items-center p-3 bg-gray-50 rounded-xl"
          >
            <div 
              class="w-10 h-10 rounded-full flex items-center justify-center mr-3"
              :class="achievement.bgColor"
            >
              <ZapIcon v-if="achievement.icon === 'zap'" size="20" :class="achievement.iconColor" />
              <BookIcon v-else-if="achievement.icon === 'book'" size="20" :class="achievement.iconColor" />
              <HeartIcon v-else-if="achievement.icon === 'heart'" size="20" :class="achievement.iconColor" />
              <AwardIcon v-else size="20" :class="achievement.iconColor" />
            </div>
            <div class="flex-1">
              <h4 class="font-medium text-gray-900">{{ achievement.title }}</h4>
              <p class="text-sm text-gray-600">{{ achievement.description }}</p>
            </div>
            <div class="text-sm font-semibold text-purple-600">+{{ achievement.xp }} XP</div>
          </div>
        </div>
      </div>
    </div>
    
    <!-- Quick Actions -->
    <div class="px-6 mb-20">
      <h3 class="text-lg font-semibold text-gray-900 mb-3">Pengaturan Akun</h3>
      <div class="bg-white rounded-2xl shadow-sm border border-gray-100 overflow-hidden">
        <button 
          @click="editProfile"
          class="w-full flex items-center p-4 hover:bg-gray-50 transition-colors duration-200"
        >
          <UserIcon size="20" class="text-gray-600 mr-3" />
          <span class="flex-1 text-left text-gray-900">Edit Profil</span>
          <ChevronRightIcon size="16" class="text-gray-400" />
        </button>
        
        <div class="border-t border-gray-100">
          <button 
            @click="manageNotifications"
            class="w-full flex items-center p-4 hover:bg-gray-50 transition-colors duration-200"
          >
            <BellIcon size="20" class="text-gray-600 mr-3" />
            <span class="flex-1 text-left text-gray-900">Notifikasi</span>
            <ChevronRightIcon size="16" class="text-gray-400" />
          </button>
        </div>
        
        <div class="border-t border-gray-100">
          <button 
            @click="viewPrivacySettings"
            class="w-full flex items-center p-4 hover:bg-gray-50 transition-colors duration-200"
          >
            <ShieldIcon size="20" class="text-gray-600 mr-3" />
            <span class="flex-1 text-left text-gray-900">Privasi & Keamanan</span>
            <ChevronRightIcon size="16" class="text-gray-400" />
          </button>
        </div>
        
        <div class="border-t border-gray-100">
          <button 
            @click="exportData"
            class="w-full flex items-center p-4 hover:bg-gray-50 transition-colors duration-200"
          >
            <DownloadIcon size="20" class="text-gray-600 mr-3" />
            <span class="flex-1 text-left text-gray-900">Ekspor Data</span>
            <ChevronRightIcon size="16" class="text-gray-400" />
          </button>
        </div>
        
        <div class="border-t border-gray-100">
          <button 
            @click="showHelp"
            class="w-full flex items-center p-4 hover:bg-gray-50 transition-colors duration-200"
          >
            <HelpCircleIcon size="20" class="text-gray-600 mr-3" />
            <span class="flex-1 text-left text-gray-900">Bantuan & Support</span>
            <ChevronRightIcon size="16" class="text-gray-400" />
          </button>
        </div>
        
        <div class="border-t border-gray-100">
          <button 
            @click="logout"
            class="w-full flex items-center p-4 hover:bg-red-50 transition-colors duration-200"
          >
            <LogOutIcon size="20" class="text-red-600 mr-3" />
            <span class="flex-1 text-left text-red-600">Keluar</span>
            <ChevronRightIcon size="16" class="text-red-400" />
          </button>
        </div>
      </div>
    </div>
    
    <!-- Settings Modal -->
    <div v-if="showSettings" class="fixed inset-0 bg-black/50 flex items-end z-50">
      <div class="bg-white w-full max-h-[70vh] rounded-t-3xl p-6 animate-slide-up">
        <div class="flex items-center justify-between mb-6">
          <h3 class="text-xl font-semibold text-gray-900">Pengaturan</h3>
          <button @click="showSettings = false" class="p-2 hover:bg-gray-100 rounded-full">
            <XIcon size="24" class="text-gray-600" />
          </button>
        </div>
        
        <div class="space-y-4">
          <!-- Theme Settings -->
          <div class="flex items-center justify-between p-4 bg-gray-50 rounded-xl">
            <div class="flex items-center">
              <MoonIcon size="20" class="text-gray-600 mr-3" />
              <div>
                <div class="font-medium text-gray-900">Mode Gelap</div>
                <div class="text-sm text-gray-600">Tema gelap untuk mata</div>
              </div>
            </div>
            <input 
              v-model="settings.darkMode"
              type="checkbox" 
              class="h-5 w-5 text-purple-600 focus:ring-purple-500 border-gray-300 rounded"
            />
          </div>
          
          <!-- Notification Settings -->
          <div class="flex items-center justify-between p-4 bg-gray-50 rounded-xl">
            <div class="flex items-center">
              <BellIcon size="20" class="text-gray-600 mr-3" />
              <div>
                <div class="font-medium text-gray-900">Notifikasi Push</div>
                <div class="text-sm text-gray-600">Pengingat dan update</div>
              </div>
            </div>
            <input 
              v-model="settings.notifications"
              type="checkbox" 
              class="h-5 w-5 text-purple-600 focus:ring-purple-500 border-gray-300 rounded"
            />
          </div>
          
          <!-- Data Usage -->
          <div class="flex items-center justify-between p-4 bg-gray-50 rounded-xl">
            <div class="flex items-center">
              <WifiIcon size="20" class="text-gray-600 mr-3" />
              <div>
                <div class="font-medium text-gray-900">Hemat Data</div>
                <div class="text-sm text-gray-600">Kurangi penggunaan data</div>
              </div>
            </div>
            <input 
              v-model="settings.dataSaver"
              type="checkbox" 
              class="h-5 w-5 text-purple-600 focus:ring-purple-500 border-gray-300 rounded"
            />
          </div>
        </div>
      </div>
    </div>
    
    <!-- Bottom Navigation -->
    <BottomNavigation />
  </div>
</template>

<script setup>
import { ref, reactive, computed } from 'vue'

// Meta untuk halaman profile
useHead({
  title: 'Profil - RealMe',
  meta: [
    { name: 'description', content: 'Kelola profil, lihat progress, dan atur pengaturan akun RealMe.' }
  ]
})

const showSettings = ref(false)

// User profile data
const userProfile = reactive({
  name: 'Jane Poe',
  email: 'jane@example.com',
  joinDate: '15 Juli 2025'
})

// User stats
const userStats = reactive({
  journalDays: 25,
  completedModules: 3,
  totalXP: 450,
  currentStreak: 7
})

// Settings
const settings = reactive({
  darkMode: false,
  notifications: true,
  dataSaver: false
})

// Recent achievements
const recentAchievements = ref([
  {
    id: 1,
    title: 'Journal Streak Master',
    description: 'Journal 7 hari berturut-turut',
    icon: 'zap',
    bgColor: 'bg-orange-100',
    iconColor: 'text-orange-600',
    xp: 50
  },
  {
    id: 2,
    title: 'Self-Acceptance Learner',
    description: 'Selesaikan 3 modul pembelajaran',
    icon: 'book',
    bgColor: 'bg-green-100',
    iconColor: 'text-green-600',
    xp: 75
  },
  {
    id: 3,
    title: 'Community Helper',
    description: 'Memberikan 10 dukungan di komunitas',
    icon: 'heart',
    bgColor: 'bg-pink-100',
    iconColor: 'text-pink-600',
    xp: 30
  }
])

// Computed
const userInitials = computed(() => {
  return userProfile.name
    .split(' ')
    .map(name => name.charAt(0))
    .join('')
    .toUpperCase()
})

const currentLevel = computed(() => {
  return Math.floor(userStats.totalXP / 100) + 1
})

// Methods
const editProfile = () => {
  console.log('Edit profile')
  // Navigate to edit profile page
}

const manageNotifications = () => {
  console.log('Manage notifications')
  // Navigate to notifications settings page
}

const viewPrivacySettings = () => {
  console.log('Privacy settings')
  // Navigate to privacy settings page
}

const exportData = () => {
  console.log('Export data')
  alert('Data kamu akan dikirim ke email dalam 24 jam.')
}

const showHelp = () => {
  console.log('Show help')
  // Navigate to help page
}

const logout = async () => {
  const confirmed = confirm('Apakah kamu yakin ingin keluar?')
  if (confirmed) {
    // Clear user data and redirect to login
    await navigateTo('/')
  }
}
</script>

<style scoped>
.animate-slide-up {
  animation: slideUp 0.3s ease-out;
}

@keyframes slideUp {
  from {
    transform: translateY(100%);
  }
  to {
    transform: translateY(0);
  }
}
</style>
