<template>
  <div class="min-h-screen bg-gray-50">
    <!-- Header -->
    <div class="safe-area-top bg-white shadow-sm">
      <div class="px-6 py-4">
        <div class="flex items-center justify-between">
          <div>
            <h1 class="text-xl font-bold text-gray-900">Halo, {{ userName }}! 👋</h1>
            <p class="text-sm text-gray-600">{{ currentDate }}</p>
          </div>
          <button @click="$router.push('/profile')" class="p-2 bg-purple-100 rounded-full">
            <UserIcon size="24" class="text-purple-600" />
          </button>
        </div>
      </div>
    </div>
    
    <!-- Quick Mood Check -->
    <div class="px-6 py-4">
      <div class="bg-gradient-to-r from-purple-500 to-pink-500 rounded-2xl p-6 text-white">
        <h2 class="text-lg font-semibold mb-3">Bagaimana perasaanmu hari ini?</h2>
        <div class="flex justify-between">
          <button 
            v-for="mood in quickMoods" 
            :key="mood.id"
            @click="selectQuickMood(mood.id)"
            class="flex flex-col items-center p-2 rounded-xl hover:bg-white/20 transition-colors duration-200"
            :class="selectedQuickMood === mood.id ? 'bg-white/20' : ''"
          >
            <span class="text-2xl mb-1">{{ mood.emoji }}</span>
            <span class="text-xs">{{ mood.label }}</span>
          </button>
        </div>
      </div>
    </div>
    
    <!-- Daily Challenge -->
    <div class="px-6 mb-4">
      <div class="bg-white rounded-2xl p-6 shadow-sm border border-gray-100">
        <div class="flex items-center justify-between mb-4">
          <div class="flex items-center">
            <div class="w-10 h-10 bg-yellow-100 rounded-full flex items-center justify-center mr-3">
              <StarIcon size="20" class="text-yellow-600" />
            </div>
            <div>
              <h3 class="font-semibold text-gray-900">Tantangan Hari Ini</h3>
              <p class="text-sm text-gray-600">Tantangan positif untukmu</p>
            </div>
          </div>
          <div class="text-right">
            <div class="text-xs text-gray-500">Reward</div>
            <div class="text-sm font-semibold text-purple-600">+10 XP</div>
          </div>
        </div>
        <div class="bg-yellow-50 rounded-xl p-4 mb-4">
          <h4 class="font-medium text-gray-900 mb-2">{{ todayChallenge.title }}</h4>
          <p class="text-sm text-gray-700">{{ todayChallenge.description }}</p>
        </div>
        <button 
          @click="completeChallenge"
          :disabled="todayChallenge.completed"
          class="w-full py-3 px-4 rounded-xl font-medium transition-colors duration-200"
          :class="todayChallenge.completed ? 'bg-green-100 text-green-700' : 'bg-yellow-600 hover:bg-yellow-700 text-white'"
        >
          <CheckCircleIcon 
            v-if="todayChallenge.completed"
            size="16" 
            class="inline mr-2" 
          />
          <PlayCircleIcon 
            v-else
            size="16" 
            class="inline mr-2" 
          />
          {{ todayChallenge.completed ? 'Selesai!' : 'Mulai Tantangan' }}
        </button>
      </div>
    </div>
    
    <!-- Quick Actions -->
    <div class="px-6 mb-4">
      <h3 class="text-lg font-semibold text-gray-900 mb-3">Aktivitas Hari Ini</h3>
      <div class="grid grid-cols-2 gap-3">
        <NuxtLink to="/journal" class="bg-white rounded-xl p-4 shadow-sm border border-gray-100 hover:shadow-md transition-shadow duration-200">
          <div class="w-10 h-10 bg-blue-100 rounded-full flex items-center justify-center mb-3">
            <BookOpenIcon size="20" class="text-blue-600" />
          </div>
          <h4 class="font-semibold text-gray-900 mb-1">Journal</h4>
          <p class="text-xs text-gray-600">Tulis perasaanmu</p>
        </NuxtLink>
        
        <NuxtLink to="/mood-tracker" class="bg-white rounded-xl p-4 shadow-sm border border-gray-100 hover:shadow-md transition-shadow duration-200">
          <div class="w-10 h-10 bg-green-100 rounded-full flex items-center justify-center mb-3">
            <SmileIcon size="20" class="text-green-600" />
          </div>
          <h4 class="font-semibold text-gray-900 mb-1">Mood</h4>
          <p class="text-xs text-gray-600">Pantau suasana hati</p>
        </NuxtLink>
        
        <NuxtLink to="/learning" class="bg-white rounded-xl p-4 shadow-sm border border-gray-100 hover:shadow-md transition-shadow duration-200">
          <div class="w-10 h-10 bg-purple-100 rounded-full flex items-center justify-center mb-3">
            <BookIcon size="20" class="text-purple-600" />
          </div>
          <h4 class="font-semibold text-gray-900 mb-1">Belajar</h4>
          <p class="text-xs text-gray-600">Modul self-acceptance</p>
        </NuxtLink>
        
        <NuxtLink to="/community" class="bg-white rounded-xl p-4 shadow-sm border border-gray-100 hover:shadow-md transition-shadow duration-200">
          <div class="w-10 h-10 bg-pink-100 rounded-full flex items-center justify-center mb-3">
            <UsersIcon size="20" class="text-pink-600" />
          </div>
          <h4 class="font-semibold text-gray-900 mb-1">Komunitas</h4>
          <p class="text-xs text-gray-600">Berbagi & support</p>
        </NuxtLink>
      </div>
    </div>
    
    <!-- Progress Overview -->
    <div class="px-6 mb-20">
      <div class="bg-white rounded-2xl p-6 shadow-sm border border-gray-100">
        <h3 class="text-lg font-semibold text-gray-900 mb-4">Progress Minggu Ini</h3>
        
        <div class="space-y-4">
          <!-- Journal Streak -->
          <div class="flex items-center justify-between">
            <div class="flex items-center">
              <div class="w-8 h-8 bg-blue-100 rounded-full flex items-center justify-center mr-3">
                <Edit3Icon size="16" class="text-blue-600" />
              </div>
              <div>
                <div class="font-medium text-gray-900">Journal Streak</div>
                <div class="text-sm text-gray-600">{{ journalStreak }} hari berturut-turut</div>
              </div>
            </div>
            <div class="text-2xl">🔥</div>
          </div>
          
          <!-- Weekly Progress -->
          <div class="flex items-center justify-between">
            <div class="flex items-center">
              <div class="w-8 h-8 bg-green-100 rounded-full flex items-center justify-center mr-3">
                <TargetIcon size="16" class="text-green-600" />
              </div>
              <div>
                <div class="font-medium text-gray-900">Target Mingguan</div>
                <div class="text-sm text-gray-600">{{ weeklyProgress }}/7 aktivitas</div>
              </div>
            </div>
            <div class="w-16 h-2 bg-gray-200 rounded-full">
              <div 
                class="h-2 bg-green-500 rounded-full transition-all duration-300"
                :style="{ width: `${(weeklyProgress / 7) * 100}%` }"
              ></div>
            </div>
          </div>
          
          <!-- Total XP -->
          <div class="flex items-center justify-between">
            <div class="flex items-center">
              <div class="w-8 h-8 bg-purple-100 rounded-full flex items-center justify-center mr-3">
                <ZapIcon size="16" class="text-purple-600" />
              </div>
              <div>
                <div class="font-medium text-gray-900">Total XP</div>
                <div class="text-sm text-gray-600">Level {{ currentLevel }}</div>
              </div>
            </div>
            <div class="text-lg font-bold text-purple-600">{{ totalXP }} XP</div>
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

// Meta untuk dashboard
useHead({
  title: 'Dashboard - RealMe',
  meta: [
    { name: 'description', content: 'Dashboard utama RealMe - pantau progress self-acceptance dan akses fitur-fitur utama aplikasi.' }
  ]
})

// Data user (simulasi)
const userName = ref('Rafi')
const currentDate = computed(() => {
  const today = new Date()
  const options = { 
    weekday: 'long', 
    year: 'numeric', 
    month: 'long', 
    day: 'numeric' 
  }
  return today.toLocaleDateString('id-ID', options)
})

// Quick mood selection
const quickMoods = [
  { id: 1, emoji: '😊', label: 'Bahagia' },
  { id: 2, emoji: '😌', label: 'Tenang' },
  { id: 3, emoji: '😔', label: 'Sedih' },
  { id: 4, emoji: '😰', label: 'Cemas' },
  { id: 5, emoji: '😡', label: 'Kesal' }
]

const selectedQuickMood = ref(null)

// Daily challenge
const todayChallenge = reactive({
  title: '3 Hal yang Aku Sukai dari Diriku',
  description: 'Tulis 3 hal yang kamu sukai dari dirimu sendiri, bisa dari fisik, kepribadian, atau pencapaianmu.',
  completed: false,
  reward: 10
})

// Progress data
const journalStreak = ref(5)
const weeklyProgress = ref(4)
const totalXP = ref(250)
const currentLevel = computed(() => Math.floor(totalXP.value / 100) + 1)

// Methods
const selectQuickMood = (moodId) => {
  selectedQuickMood.value = moodId
  // Simulasi save mood
  console.log('Mood selected:', moodId)
}

const completeChallenge = () => {
  if (!todayChallenge.completed) {
    todayChallenge.completed = true
    totalXP.value += todayChallenge.reward
    weeklyProgress.value++
    
    // Show success message
    alert('Selamat! Kamu mendapat +10 XP!')
  }
}
</script>
