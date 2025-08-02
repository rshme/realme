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

    <!-- Daily Quote -->
    <div class="px-6 mb-4">
      <div class="bg-white rounded-2xl p-6 shadow-sm border border-indigo-100 relative overflow-hidden">
        
        <div class="relative z-10">
          <div class="flex items-start justify-between mb-5">
            <div class="flex items-center">
              <div>
                <h3 class="text-lg font-bold text-gray-900">Quotes Hari Ini</h3>
                <p class="text-sm text-gray-600">Inspirasi untuk harimu</p>
              </div>
            </div>
            <button 
              @click="refreshQuote" 
              class="p-2 bg-white/70 hover:bg-white rounded-full shadow-sm transition-all duration-200 hover:scale-105"
            >
              <svg class="w-5 h-5 text-indigo-600" :class="{ 'animate-spin': isRefreshingQuote }" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"></path>
              </svg>
            </button>
          </div>
          
          <div class="relative">
            <!-- Quote mark decoration -->
            <div class="absolute -top-2 -left-1 text-4xl text-indigo-200 font-serif">"</div>
            
            <blockquote class="text-gray-900 text-lg font-medium leading-relaxed mb-4 pl-6 pr-2">
              {{ dailyQuote.text }}
            </blockquote>
            
            <div class="flex items-center justify-between pl-6">
              <cite class="text-sm text-indigo-600 font-semibold not-italic">
                — {{ dailyQuote.author }}
              </cite>
              <div class="flex items-center space-x-2">
                <button 
                  @click="toggleFavoriteQuote"
                  class="p-2 rounded-full hover:bg-indigo-100 transition-colors duration-200"
                  :class="dailyQuote.isFavorite ? 'text-red-500' : 'text-gray-400'"
                >
                  <svg class="w-5 h-5" :fill="dailyQuote.isFavorite ? 'currentColor' : 'none'" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4.318 6.318a4.5 4.5 0 000 6.364L12 20.364l7.682-7.682a4.5 4.5 0 00-6.364-6.364L12 7.636l-1.318-1.318a4.5 4.5 0 00-6.364 0z"></path>
                  </svg>
                </button>
                <button 
                  @click="shareQuote"
                  class="p-2 rounded-full hover:bg-indigo-100 transition-colors duration-200 text-gray-400 hover:text-indigo-600"
                >
                  <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8.684 13.342C8.886 12.938 9 12.482 9 12c0-.482-.114-.938-.316-1.342m0 2.684a3 3 0 110-2.684m0 2.684l6.632 3.316m-6.632-6l6.632-3.316m0 0a3 3 0 105.367-2.684 3 3 0 00-5.367 2.684zm0 9.316a3 3 0 105.367 2.684 3 3 0 00-5.367-2.684z"></path>
                  </svg>
                </button>
              </div>
            </div>
          </div>
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
    <div class="px-6 pb-28">
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
const userName = ref('Jane')
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

// Daily quotes data
const quotes = [
  {
    text: "Kamu tidak perlu menjadi sempurna. Kamu hanya perlu menjadi dirimu sendiri.",
    author: "Unknown"
  },
  {
    text: "Self-acceptance adalah kunci pertama menuju kebahagiaan yang sejati.",
    author: "Unknown"  
  },
  {
    text: "Cintai dirimu apa adanya, karena tidak ada yang bisa menggantikan keunikanmu.",
    author: "Unknown"
  },
  {
    text: "Setiap hari adalah kesempatan baru untuk menerima dan mencintai dirimu lebih dalam.",
    author: "Unknown"
  },
  {
    text: "Kamu sudah cukup, bahkan ketika kamu merasa belum sempurna.",
    author: "Unknown"
  },
  {
    text: "Perjalanan menerima diri sendiri dimulai dengan satu langkah kecil setiap hari.",
    author: "Unknown"
  },
  {
    text: "Jangan membandingkan chapter 1 hidupmu dengan chapter 20 orang lain.",
    author: "Unknown"
  }
]

const dailyQuote = reactive({
  text: "",
  author: "",
  isFavorite: false
})

const isRefreshingQuote = ref(false)

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

const getRandomQuote = () => {
  const randomIndex = Math.floor(Math.random() * quotes.length)
  const quote = quotes[randomIndex]
  dailyQuote.text = quote.text
  dailyQuote.author = quote.author
  dailyQuote.isFavorite = false
}

const refreshQuote = async () => {
  isRefreshingQuote.value = true
  
  // Simulate API call delay
  await new Promise(resolve => setTimeout(resolve, 500))
  
  getRandomQuote()
  isRefreshingQuote.value = false
}

const toggleFavoriteQuote = () => {
  dailyQuote.isFavorite = !dailyQuote.isFavorite
  
  if (dailyQuote.isFavorite) {
    // Could save to favorites list
    console.log('Quote added to favorites')
  } else {
    console.log('Quote removed from favorites')
  }
}

const shareQuote = () => {
  const shareText = `"${dailyQuote.text}" - ${dailyQuote.author}`
  
  if (navigator.share) {
    navigator.share({
      title: 'Quote Inspiratif dari RealMe',
      text: shareText,
      url: window.location.origin
    })
  } else {
    // Fallback: copy to clipboard
    navigator.clipboard.writeText(shareText).then(() => {
      alert('Quote berhasil disalin ke clipboard!')
    })
  }
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

// Initialize daily quote when component mounts
getRandomQuote()
</script>
