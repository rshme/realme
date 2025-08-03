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

    <!-- Graph Report Section -->
    <div class="px-6 mb-6">
      <h3 class="text-lg font-semibold text-gray-900 mb-3">Laporan Progress</h3>
      
      <!-- Period Tabs -->
      <div class="mb-4">
        <div class="flex space-x-1 bg-gray-100 rounded-xl p-1">
          <button 
            @click="activeGraphPeriod = 'weekly'"
            class="flex-1 py-2 px-4 rounded-lg font-medium text-sm transition-all duration-200"
            :class="activeGraphPeriod === 'weekly' ? 'bg-white text-purple-600 shadow-sm' : 'text-gray-600 hover:text-gray-900'"
          >
            <CalendarIcon size="16" class="inline mr-2" />
            Mingguan
          </button>
          <button 
            @click="activeGraphPeriod = 'monthly'"
            class="flex-1 py-2 px-4 rounded-lg font-medium text-sm transition-all duration-200"
            :class="activeGraphPeriod === 'monthly' ? 'bg-white text-purple-600 shadow-sm' : 'text-gray-600 hover:text-gray-900'"
          >
            <BarChartIcon size="16" class="inline mr-2" />
            Bulanan
          </button>
        </div>
      </div>

      <!-- Achievements Graph -->
      <div class="bg-white rounded-2xl p-4 shadow-sm border border-gray-100 mb-4">
        <div class="flex items-center justify-between mb-4">
          <h4 class="font-semibold text-gray-900">Pencapaian</h4>
          <div class="flex items-center text-sm text-gray-600">
            <ZapIcon size="16" class="mr-2" />
            <span>{{ activeGraphPeriod === 'weekly' ? 'Per Hari' : 'Per Minggu' }}</span>
          </div>
        </div>
        
        <!-- Achievement Bar Chart -->
        <div class="relative">
          <svg class="w-full h-48" viewBox="0 0 400 200" xmlns="http://www.w3.org/2000/svg">
            <!-- Grid lines -->
            <defs>
              <pattern id="grid" width="40" height="40" patternUnits="userSpaceOnUse">
                <path d="M 40 0 L 0 0 0 40" fill="none" stroke="#f3f4f6" stroke-width="1"/>
              </pattern>
            </defs>
            <rect width="100%" height="100%" fill="url(#grid)" />
            
            <!-- Y-axis labels -->
            <g class="text-xs fill-gray-500">
              <text x="30" y="35" text-anchor="end">{{ maxAchievements }}</text>
              <text x="30" y="65" text-anchor="end">{{ Math.round(maxAchievements * 0.75) }}</text>
              <text x="30" y="105" text-anchor="end">{{ Math.round(maxAchievements * 0.5) }}</text>
              <text x="30" y="135" text-anchor="end">{{ Math.round(maxAchievements * 0.25) }}</text>
              <text x="30" y="185" text-anchor="end">0</text>
            </g>
            
            <!-- Achievement bars -->
            <g v-for="(item, index) in currentAchievementData" :key="`achievement-${index}`">
              <rect
                :x="40 + index * 48"
                :y="180 - (item.value / maxAchievements) * 160"
                width="32"
                :height="(item.value / maxAchievements) * 160"
                :class="getAchievementBarColor(item.value)"
                rx="4"
                class="transition-all duration-300 hover:opacity-80"
              />
              <!-- Value label on top of bar -->
              <text
                :x="40 + index * 48 + 16"
                :y="180 - (item.value / maxAchievements) * 160 - 5"
                text-anchor="middle"
                class="text-xs fill-gray-700 font-semibold"
              >
                {{ item.value }}
              </text>
            </g>
            
            <!-- X-axis labels -->
            <g class="text-xs fill-gray-600">
              <text
                v-for="(item, index) in currentAchievementData"
                :key="`x-label-${index}`"
                :x="40 + index * 48 + 16"
                y="198"
                text-anchor="middle"
                class="font-medium"
              >
                {{ item.label }}
              </text>
            </g>
          </svg>
        </div>
      </div>

      <!-- Moods Graph -->
      <div class="bg-white rounded-2xl p-4 shadow-sm border border-gray-100">
        <div class="flex items-center justify-between mb-4">
          <h4 class="font-semibold text-gray-900">Mood Tracker</h4>
          <div class="flex items-center space-x-4 text-sm text-gray-600">
            <div class="flex items-center">
              <span class="w-3 h-3 bg-red-400 rounded-full mr-2"></span>
              <span>😠 1-3</span>
            </div>
            <div class="flex items-center">
              <span class="w-3 h-3 bg-yellow-400 rounded-full mr-2"></span>
              <span>😐 4-6</span>
            </div>
            <div class="flex items-center">
              <span class="w-3 h-3 bg-green-400 rounded-full mr-2"></span>
              <span>😊 7-10</span>
            </div>
          </div>
        </div>
        
        <!-- Mood Line Chart -->
        <div class="relative">
          <svg class="w-full h-48" viewBox="0 0 400 200" xmlns="http://www.w3.org/2000/svg">
            <!-- Grid lines -->
            <rect width="100%" height="100%" fill="url(#grid)" />
            
            <!-- Y-axis labels with emojis -->
            <g class="text-xs fill-gray-500">
              <text x="30" y="35" text-anchor="end">10</text>
              <text x="15" y="35" text-anchor="end">😊</text>
              <text x="30" y="65" text-anchor="end">8</text>
              <text x="30" y="95" text-anchor="end">6</text>
              <text x="15" y="95" text-anchor="end">😐</text>
              <text x="30" y="125" text-anchor="end">4</text>
              <text x="30" y="155" text-anchor="end">2</text>
              <text x="15" y="155" text-anchor="end">😠</text>
              <text x="30" y="185" text-anchor="end">0</text>
            </g>
            
            <!-- Mood line path -->
            <path
              :d="moodLinePath"
              fill="none"
              stroke="url(#moodGradient)"
              stroke-width="3"
              stroke-linecap="round"
              stroke-linejoin="round"
              class="transition-all duration-500"
            />
            
            <!-- Gradient definition for mood line -->
            <defs>
              <linearGradient id="moodGradient" x1="0%" y1="0%" x2="100%" y2="0%">
                <stop offset="0%" style="stop-color:#ef4444;stop-opacity:1" />
                <stop offset="40%" style="stop-color:#f59e0b;stop-opacity:1" />
                <stop offset="70%" style="stop-color:#10b981;stop-opacity:1" />
                <stop offset="100%" style="stop-color:#06b6d4;stop-opacity:1" />
              </linearGradient>
            </defs>
            
            <!-- Mood data points -->
            <g v-for="(item, index) in currentMoodData" :key="`mood-${index}`">
              <circle
                :cx="40 + index * 48"
                :cy="180 - (item.value / 10) * 160"
                r="5"
                :class="getMoodPointColor(item.value)"
                class="stroke-white stroke-2 transition-all duration-300 hover:r-7"
              />
              <!-- Mood emoji -->
              <text
                :x="40 + index * 48"
                :y="180 - (item.value / 10) * 160 - 15"
                text-anchor="middle"
                class="text-sm"
              >
                {{ getMoodEmoji(item.value) }}
              </text>
            </g>
            
            <!-- X-axis labels -->
            <g class="text-xs fill-gray-600">
              <text
                v-for="(item, index) in currentMoodData"
                :key="`mood-x-label-${index}`"
                :x="40 + index * 48"
                y="198"
                text-anchor="middle"
                class="font-medium"
              >
                {{ item.label }}
              </text>
            </g>
          </svg>
        </div>
      </div>
    </div>

    <!-- Wellness Insight Section -->
    <div class="px-6 mb-6">
      <h3 class="text-lg font-semibold text-gray-900 mb-3">Insight Kesehatan Mental</h3>
      <div class="bg-gradient-to-br from-blue-50 to-indigo-100 rounded-2xl p-5 border border-blue-200">
        <div class="flex items-start">
          <div class="w-12 h-12 bg-blue-500 rounded-full flex items-center justify-center mr-4 flex-shrink-0">
            <svg class="w-6 h-6 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9.663 17h4.673M12 3v1m6.364 1.636l-.707.707M21 12h-1M4 12H3m3.343-5.657l-.707-.707m2.828 9.9a5 5 0 117.072 0l-.548.547A3.374 3.374 0 0014 18.469V19a2 2 0 11-4 0v-.531c0-.895-.356-1.754-.988-2.386l-.548-.547z"></path>
            </svg>
          </div>
          <div class="flex-1">
            <h4 class="font-semibold text-gray-900 mb-2">{{ currentInsight.title }}</h4>
            <p class="text-gray-700 text-sm leading-relaxed mb-3">{{ currentInsight.description }}</p>
            <div class="flex items-center text-xs text-blue-700">
              <svg class="w-4 h-4 mr-1" fill="currentColor" viewBox="0 0 20 20">
                <path fill-rule="evenodd" d="M18 10a8 8 0 11-16 0 8 8 0 0116 0zm-7-4a1 1 0 11-2 0 1 1 0 012 0zM9 9a1 1 0 000 2v3a1 1 0 001 1h1a1 1 0 100-2v-3a1 1 0 00-1-1H9z" clip-rule="evenodd"></path>
              </svg>
              <span>Berdasarkan aktivitas 7 hari terakhir</span>
            </div>
            <!-- Insight Actions -->
            <div class="mt-4 flex flex-wrap gap-2">
              <button 
                v-for="action in currentInsight.actions" 
                :key="action.id"
                @click="performInsightAction(action.id)"
                class="flex items-center px-3 py-2 bg-white/70 backdrop-blur-sm rounded-lg text-sm font-medium text-blue-700 hover:bg-white/90 transition-all duration-200"
              >
                <component :is="action.icon" size="16" class="mr-2" />
                {{ action.label }}
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Goals & Preferences Section -->
    <div class="px-6 mb-6">
      <div class="flex items-center justify-between mb-3">
        <h3 class="text-lg font-semibold text-gray-900">Goals & Preferensi</h3>
        <button 
          @click="editGoals"
          class="text-purple-600 text-sm font-medium hover:text-purple-700 transition-colors duration-200"
        >
          Edit
        </button>
      </div>
      
      <!-- Current Goals -->
      <div class="bg-white rounded-2xl p-4 shadow-sm border border-gray-100 mb-4">
        <h4 class="font-semibold text-gray-900 mb-3 flex items-center">
          <svg class="w-5 h-5 text-green-600 mr-2" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z"></path>
          </svg>
          Target Bulanan
        </h4>
        <div class="space-y-3">
          <div 
            v-for="goal in currentGoals" 
            :key="goal.id"
            class="flex items-center justify-between p-3 bg-gray-50 rounded-xl"
          >
            <div class="flex items-center flex-1">
              <div 
                class="w-8 h-8 rounded-full flex items-center justify-center mr-3"
                :class="goal.iconBg"
              >
                <component :is="goal.icon" size="16" :class="goal.iconColor" />
              </div>
              <div class="flex-1">
                <div class="font-medium text-gray-900">{{ goal.title }}</div>
                <div class="text-sm text-gray-600">{{ goal.current }}/{{ goal.target }} {{ goal.unit }}</div>
              </div>
            </div>
            <div class="flex items-center">
              <div class="w-16 h-2 bg-gray-200 rounded-full mr-3">
                <div 
                  class="h-2 rounded-full transition-all duration-300"
                  :class="goal.progressColor"
                  :style="{ width: Math.min((goal.current / goal.target) * 100, 100) + '%' }"
                ></div>
              </div>
              <span class="text-sm font-semibold text-gray-700">{{ Math.round((goal.current / goal.target) * 100) }}%</span>
            </div>
          </div>
        </div>
      </div>
    </div>
    
    <!-- Quick Actions -->
    <div class="px-6 mb-20 pb-6">
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
  
    </div>

    <!-- Bottom Navigation -->
    <BottomNavigation />
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
const activeGraphPeriod = ref('weekly')

// Graph data
const weeklyAchievements = ref([
  { label: 'Sen', value: 2 },
  { label: 'Sel', value: 1 },
  { label: 'Rab', value: 3 },
  { label: 'Kam', value: 0 },
  { label: 'Jum', value: 2 },
  { label: 'Sab', value: 1 },
  { label: 'Min', value: 4 }
])

const monthlyAchievements = ref([
  { label: 'W1', value: 8 },
  { label: 'W2', value: 12 },
  { label: 'W3', value: 6 },
  { label: 'W4', value: 15 }
])

const weeklyMoods = ref([
  { label: 'Sen', value: 7 },
  { label: 'Sel', value: 5 },
  { label: 'Rab', value: 8 },
  { label: 'Kam', value: 3 },
  { label: 'Jum', value: 6 },
  { label: 'Sab', value: 9 },
  { label: 'Min', value: 8 }
])

const monthlyMoods = ref([
  { label: 'W1', value: 6.5 },
  { label: 'W2', value: 7.2 },
  { label: 'W3', value: 5.8 },
  { label: 'W4', value: 8.1 }
])

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

// Wellness insight data
const currentInsight = ref({
  title: 'Mood Stabil & Positif',
  description: 'Mood kamu menunjukkan tren positif minggu ini! Konsistensi dalam journaling dan aktivitas self-care memberikan dampak baik pada kesehatan mental kamu.',
  actions: [
    {
      id: 'journal',
      label: 'Lanjut Journal',
      icon: 'Edit3Icon'
    },
    {
      id: 'meditation',
      label: 'Meditasi',
      icon: 'ZapIcon'
    },
    {
      id: 'community',
      label: 'Share di Komunitas',
      icon: 'HeartIcon'
    }
  ]
})

// Current goals data
const currentGoals = ref([
  {
    id: 1,
    title: 'Journaling Harian',
    current: 18,
    target: 30,
    unit: 'hari',
    icon: 'Edit3Icon',
    iconBg: 'bg-blue-100',
    iconColor: 'text-blue-600',
    progressColor: 'bg-blue-500'
  },
  {
    id: 2,
    title: 'Mood Tracking',
    current: 22,
    target: 30,
    unit: 'hari',
    icon: 'HeartIcon',
    iconBg: 'bg-pink-100',
    iconColor: 'text-pink-600',
    progressColor: 'bg-pink-500'
  },
  {
    id: 3,
    title: 'Modul Learning',
    current: 2,
    target: 4,
    unit: 'modul',
    icon: 'BookIcon',
    iconBg: 'bg-green-100',
    iconColor: 'text-green-600',
    progressColor: 'bg-green-500'
  },
  {
    id: 4,
    title: 'Community Support',
    current: 7,
    target: 15,
    unit: 'interaksi',
    icon: 'UserIcon',
    iconBg: 'bg-purple-100',
    iconColor: 'text-purple-600',
    progressColor: 'bg-purple-500'
  }
])

// User preferences data
const userPreferences = ref([
  {
    id: 1,
    title: 'Morning Person',
    subtitle: 'Aktivitas pagi',
    icon: 'SunIcon',
    active: true
  },
  {
    id: 2,
    title: 'Mindfulness',
    subtitle: 'Fokus & tenang',
    icon: 'ZapIcon',
    active: true
  },
  {
    id: 3,
    title: 'Social Support',
    subtitle: 'Dukungan sosial',
    icon: 'UsersIcon',
    active: false
  },
  {
    id: 4,
    title: 'Creative Expression',
    subtitle: 'Ekspresi kreatif',
    icon: 'Edit3Icon',
    active: true
  },
  {
    id: 5,
    title: 'Physical Activity',
    subtitle: 'Aktivitas fisik',
    icon: 'ZapIcon',
    active: false
  },
  {
    id: 6,
    title: 'Reading & Learning',
    subtitle: 'Belajar & membaca',
    icon: 'BookIcon',
    active: true
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

// Graph computed properties
const currentAchievementData = computed(() => {
  return activeGraphPeriod.value === 'weekly' ? weeklyAchievements.value : monthlyAchievements.value
})

const currentMoodData = computed(() => {
  return activeGraphPeriod.value === 'weekly' ? weeklyMoods.value : monthlyMoods.value
})

const maxAchievements = computed(() => {
  const data = currentAchievementData.value
  return Math.max(...data.map(d => d.value)) + 2
})

const moodLinePath = computed(() => {
  const data = currentMoodData.value
  let path = ''
  
  data.forEach((item, index) => {
    const x = 40 + index * 48
    const y = 180 - (item.value / 10) * 160
    
    if (index === 0) {
      path += `M ${x} ${y}`
    } else {
      path += ` L ${x} ${y}`
    }
  })
  
  return path
})

// Methods
const getAchievementBarColor = (value) => {
  if (value === 0) return 'fill-gray-200'
  if (value <= 2) return 'fill-blue-400'
  if (value <= 4) return 'fill-purple-500'
  return 'fill-green-500'
}

const getMoodPointColor = (value) => {
  if (value <= 3) return 'fill-red-500'
  if (value <= 6) return 'fill-yellow-500'
  return 'fill-green-500'
}

const getMoodEmoji = (value) => {
  if (value <= 3) return '😠'
  if (value <= 6) return '😐'
  return '😊'
}

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

const performInsightAction = (actionId) => {
  console.log('Performing insight action:', actionId)
  
  switch (actionId) {
    case 'journal':
      navigateTo('/journal')
      break
    case 'meditation':
      console.log('Starting meditation session')
      alert('Fitur meditasi akan segera hadir!')
      break
    case 'community':
      navigateTo('/community')
      break
    default:
      console.log('Unknown action:', actionId)
  }
}

const editGoals = () => {
  console.log('Edit goals')
  alert('Fitur edit goals akan segera hadir!')
  // Navigate to goals editing page or show modal
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
