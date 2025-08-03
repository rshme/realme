<template>
  <div class="min-h-screen bg-gray-50">
    <!-- Progress Bar -->
    <div class="safe-area-top bg-white shadow-sm">
      <div class="px-6 py-4">
        <div class="flex items-center justify-between mb-2">
          <span class="text-sm text-gray-600">Langkah {{ currentStep }} dari {{ totalSteps }}</span>
          <button @click="skipOnboarding" class="text-sm text-purple-600 hover:underline">Lewati</button>
        </div>
        <div class="w-full bg-gray-200 rounded-full h-2">
          <div 
            class="bg-purple-600 h-2 rounded-full transition-all duration-300"
            :style="{ width: `${(currentStep / totalSteps) * 100}%` }"
          ></div>
        </div>
      </div>
    </div>
    
    <!-- Onboarding Content -->
    <div class="p-6">
      <!-- Step 1: Welcome -->
      <div v-if="currentStep === 1" class="text-center">
        <div class="w-32 h-32 mx-auto mb-8 bg-gradient-to-br from-purple-400 to-pink-500 rounded-full flex items-center justify-center">
          <HeartIcon size="48" class="text-white" />
        </div>
        <h1 class="text-3xl font-bold text-gray-900 mb-4">Selamat Datang di RealMe!</h1>
        <p class="text-gray-600 text-lg leading-relaxed mb-8">
          Kami senang kamu bergabung dalam perjalanan menuju self-acceptance. 
          Mari kita mulai dengan mengenal dirimu lebih baik.
        </p>
        <div class="space-y-4">
          <div class="bg-white rounded-xl p-4 border border-gray-100">
            <div class="flex items-center">
              <ShieldIcon name="shield" size="24" class="text-green-600 mr-3" />
              <div class="text-left">
                <h3 class="font-semibold text-gray-900">Aman & Privat</h3>
                <p class="text-sm text-gray-600">Data pribadimu terlindungi</p>
              </div>
            </div>
          </div>
          <div class="bg-white rounded-xl p-4 border border-gray-100">
            <div class="flex items-center">
              <UsersIcon name="users" size="24" class="text-blue-600 mr-3" />
              <div class="text-left">
                <h3 class="font-semibold text-gray-900">Komunitas Supportif</h3>
                <p class="text-sm text-gray-600">Berbagi dengan teman sebaya</p>
              </div>
            </div>
          </div>
          <div class="bg-white rounded-xl p-4 border border-gray-100">
            <div class="flex items-center">
              <TrendingUpIcon size="24" class="text-purple-600 mr-3" />
              <div class="text-left">
                <h3 class="font-semibold text-gray-900">Progress Tracking</h3>
                <p class="text-sm text-gray-600">Pantau perkembangan dirimu</p>
              </div>
            </div>
          </div>
        </div>
      </div>
      
      <!-- Step 2: Goals -->
      <div v-if="currentStep === 2" class="text-center">
        <div class="w-24 h-24 mx-auto mb-6 bg-purple-100 rounded-full flex items-center justify-center">
          <TargetIcon size="32" class="text-purple-600" />
        </div>
        <h2 class="text-2xl font-bold text-gray-900 mb-4">Apa tujuan utamamu?</h2>
        <p class="text-gray-600 mb-8">Pilih yang paling sesuai dengan kondisimu saat ini</p>
        
        <div class="space-y-3">
          <button 
            v-for="goal in goals" 
            :key="goal.id"
            @click="selectGoal(goal.id)"
            class="w-full p-4 border-2 rounded-xl text-left transition-all duration-200"
            :class="selectedGoal === goal.id ? 'border-purple-600 bg-purple-50' : 'border-gray-200 hover:border-purple-300'"
          >
            <div class="flex items-center">
              <CheckCircleIcon :name="goal.icon" size="24" class="mr-3" :class="selectedGoal === goal.id ? 'text-purple-600' : 'text-gray-600'" />
              <div>
                <h3 class="font-semibold text-gray-900">{{ goal.title }}</h3>
                <p class="text-sm text-gray-600">{{ goal.description }}</p>
              </div>
            </div>
          </button>
        </div>
      </div>
      
      <!-- Step 3: Mood Check -->
      <div v-if="currentStep === 3" class="text-center">
        <div class="w-24 h-24 mx-auto mb-6 bg-blue-100 rounded-full flex items-center justify-center">
          <SmileIcon size="32" class="text-blue-600" />
        </div>
        <h2 class="text-2xl font-bold text-gray-900 mb-4">Bagaimana perasaanmu hari ini?</h2>
        <p class="text-gray-600 mb-8">Ini akan membantu kami memberikan konten yang tepat</p>
        
        <div class="grid grid-cols-3 gap-4">
          <button 
            v-for="mood in moods" 
            :key="mood.id"
            @click="selectMood(mood.id)"
            class="p-4 border-2 rounded-xl transition-all duration-200"
            :class="selectedMood === mood.id ? 'border-purple-600 bg-purple-50' : 'border-gray-200 hover:border-purple-300'"
          >
            <div class="text-3xl mb-2">{{ mood.emoji }}</div>
            <div class="text-sm font-medium text-gray-900">{{ mood.label }}</div>
          </button>
        </div>
      </div>
      
      <!-- Step 4: Notifications -->
      <div v-if="currentStep === 4" class="text-center">
        <div class="w-24 h-24 mx-auto mb-6 bg-green-100 rounded-full flex items-center justify-center">
          <BellIcon size="32" class="text-green-600" />
        </div>
        <h2 class="text-2xl font-bold text-gray-900 mb-4">Tetap terhubung</h2>
        <p class="text-gray-600 mb-8">Kami akan mengingatkanmu untuk journaling dan refleksi harian</p>
        
        <div class="space-y-4">
          <div class="bg-white rounded-xl p-4 border border-gray-100">
            <div class="flex items-center justify-between">
              <div class="flex items-center text-left">
                <SunriseIcon size="24" class="text-orange-500 mr-3" />
                <div>
                  <h3 class="font-semibold text-gray-900">Pengingat Pagi</h3>
                  <p class="text-sm text-gray-600">Mulai hari dengan afirmasi positif</p>
                </div>
              </div>
              <input 
                v-model="notifications.morning"
                type="checkbox" 
                class="h-5 w-5 text-purple-600 focus:ring-purple-500 border-gray-300 rounded"
              />
            </div>
          </div>
          
          <div class="bg-white rounded-xl p-4 border border-gray-100">
            <div class="flex items-center justify-between">
              <div class="flex items-center text-left">
                <MoonIcon size="24" class="text-blue-500 mr-3" />
                <div>
                  <h3 class="font-semibold text-gray-900">Pengingat Malam</h3>
                  <p class="text-sm text-gray-600">Refleksi hari dan journaling</p>
                </div>
              </div>
              <input 
                v-model="notifications.evening"
                type="checkbox" 
                class="h-5 w-5 text-purple-600 focus:ring-purple-500 border-gray-300 rounded"
              />
            </div>
          </div>
          
          <div class="bg-white rounded-xl p-4 border border-gray-100">
            <div class="flex items-center justify-between">
              <div class="flex items-center text-left">
                <AwardIcon size="24" class="text-purple-500 mr-3" />
                <div>
                  <h3 class="font-semibold text-gray-900">Tantangan Harian</h3>
                  <p class="text-sm text-gray-600">Daily challenges untuk self-acceptance</p>
                </div>
              </div>
              <input 
                v-model="notifications.challenges"
                type="checkbox" 
                class="h-5 w-5 text-purple-600 focus:ring-purple-500 border-gray-300 rounded"
              />
            </div>
          </div>
        </div>
      </div>
    </div>
    
    <!-- Navigation Buttons -->
    <div class="fixed bottom-0 left-0 right-0 bg-white border-t border-gray-200 safe-area-bottom">
      <div class="max-w-md mx-auto px-6 py-4 flex justify-between">
        <button 
          v-if="currentStep > 1"
          @click="previousStep"
          class="btn-secondary flex items-center"
        >
          <ArrowLeftIcon size="16" class="mr-2" />
          Kembali
        </button>
        <div v-else></div>
        
        <button 
          @click="nextStep"
          :disabled="!canProceed"
          class="btn-primary flex items-center"
          :class="!canProceed ? 'opacity-50 cursor-not-allowed' : ''"
        >
          {{ currentStep === totalSteps ? 'Mulai Perjalanan' : 'Lanjut' }}
          <ArrowRightIcon size="16" class="ml-2" />
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, computed } from 'vue'

// Meta untuk halaman onboarding
useHead({
  title: 'Onboarding - RealMe',
  meta: [
    { name: 'description', content: 'Mulai perjalanan self-acceptance dengan RealMe. Setup profil dan preferensi untuk pengalaman yang personal.' }
  ]
})

const currentStep = ref(1)
const totalSteps = 4

// Data untuk step 2 (Goals)
const goals = [
  { id: 'self-acceptance', title: 'Meningkatkan Self-Acceptance', description: 'Belajar menerima diri apa adanya', icon: 'heart' },
  { id: 'body-image', title: 'Memperbaiki Body Image', description: 'Membangun hubungan positif dengan tubuh', icon: 'user' },
  { id: 'confidence', title: 'Membangun Kepercayaan Diri', description: 'Meningkatkan rasa percaya diri', icon: 'star' },
  { id: 'stress-management', title: 'Mengelola Stres', description: 'Teknik coping untuk tekanan hidup', icon: 'shield' },
  { id: 'peer-support', title: 'Mencari Dukungan', description: 'Terhubung dengan komunitas supportif', icon: 'users' }
]

// Data untuk step 3 (Mood)
const moods = [
  { id: 1, emoji: '😊', label: 'Bahagia' },
  { id: 2, emoji: '😔', label: 'Sedih' },
  { id: 3, emoji: '😰', label: 'Cemas' },
  { id: 4, emoji: '😡', label: 'Marah' },
  { id: 5, emoji: '😴', label: 'Lelah' },
  { id: 6, emoji: '🤔', label: 'Bingung' }
]

// State
const selectedGoal = ref('')
const selectedMood = ref(null)
const notifications = reactive({
  morning: true,
  evening: true,
  challenges: true
})

// Computed
const canProceed = computed(() => {
  if (currentStep.value === 1) return true
  if (currentStep.value === 2) return selectedGoal.value !== ''
  if (currentStep.value === 3) return selectedMood.value !== null
  if (currentStep.value === 4) return true
  return false
})

// Methods
const selectGoal = (goalId) => {
  selectedGoal.value = goalId
}

const selectMood = (moodId) => {
  selectedMood.value = moodId
}

const nextStep = async () => {
  if (currentStep.value < totalSteps) {
    currentStep.value++
  } else {
    // Selesaikan onboarding dan redirect ke dashboard
    await finishOnboarding()
  }
}

const previousStep = () => {
  if (currentStep.value > 1) {
    currentStep.value--
  }
}

const skipOnboarding = async () => {
  await navigateTo('/dashboard')
}

const finishOnboarding = async () => {
  try {
    // Simulasi save onboarding data
    const onboardingData = {
      goal: selectedGoal.value,
      mood: selectedMood.value,
      notifications: notifications
    }
    
    console.log('Onboarding completed:', onboardingData)
    
    // Redirect ke dashboard
    await navigateTo('/dashboard')
  } catch (error) {
    console.error('Error finishing onboarding:', error)
  }
}
</script>
