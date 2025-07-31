<template>
  <div class="min-h-screen bg-gray-50">
    <!-- Header -->
    <div class="safe-area-top bg-white shadow-sm">
      <div class="flex items-center justify-between p-4">
        <button @click="$router.back()" class="p-2 hover:bg-gray-100 rounded-full">
          <ArrowLeftIcon size="24" class="text-gray-600" />
        </button>
        <h1 class="text-xl font-semibold text-gray-900">Daily Journal</h1>
        <button @click="showHistory = true" class="p-2 hover:bg-gray-100 rounded-full">
          <CalendarIcon size="24" class="text-gray-600" />
        </button>
      </div>
    </div>
    
    <!-- Journal Form -->
    <div class="p-6">
      <!-- Date & Streak -->
      <div class="bg-gradient-to-r from-blue-500 to-purple-600 rounded-2xl p-6 text-white mb-6">
        <div class="flex items-center justify-between">
          <div>
            <h2 class="text-lg font-semibold">{{ currentDate }}</h2>
            <p class="text-white/80">Bagaimana harimu?</p>
          </div>
          <div class="text-center">
            <div class="text-2xl mb-1">🔥</div>
            <div class="text-sm">{{ journalStreak }} hari</div>
          </div>
        </div>
      </div>
      
      <!-- Mood Selection -->
      <div class="bg-white rounded-2xl p-6 shadow-sm border border-gray-100 mb-6">
        <h3 class="text-lg font-semibold text-gray-900 mb-4">Bagaimana perasaanmu?</h3>
        <div class="grid grid-cols-4 gap-3">
          <button 
            v-for="mood in detailedMoods" 
            :key="mood.id"
            @click="selectMood(mood.id)"
            class="flex flex-col items-center p-3 rounded-xl border-2 transition-all duration-200"
            :class="selectedMood === mood.id ? 'border-purple-600 bg-purple-50' : 'border-gray-200 hover:border-purple-300'"
          >
            <span class="text-2xl mb-2">{{ mood.emoji }}</span>
            <span class="text-xs font-medium text-gray-700">{{ mood.label }}</span>
          </button>
        </div>
      </div>
      
      <!-- Reflection Questions -->
      <div class="bg-white rounded-2xl p-6 shadow-sm border border-gray-100 mb-6">
        <h3 class="text-lg font-semibold text-gray-900 mb-4">Refleksi Hari Ini</h3>
        
        <div class="space-y-4">
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-2">
              Apa yang membuatmu bersyukur hari ini?
            </label>
            <textarea 
              v-model="journal.gratitude"
              class="input-field resize-none"
              rows="3"
              placeholder="Tulis minimal 1 hal yang membuatmu bersyukur..."
            ></textarea>
          </div>
          
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-2">
              Pencapaian apa yang kamu raih hari ini? (sekecil apapun)
            </label>
            <textarea 
              v-model="journal.achievement"
              class="input-field resize-none"
              rows="3"
              placeholder="Misal: Makan teratur, menyelesaikan tugas, membantu teman..."
            ></textarea>
          </div>
          
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-2">
              Apa yang kamu sukai dari dirimu hari ini?
            </label>
            <textarea 
              v-model="journal.selfLove"
              class="input-field resize-none"
              rows="3"
              placeholder="Fokus pada hal positif tentang dirimu..."
            ></textarea>
          </div>
          
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-2">
              Cerita bebas tentang harimu
            </label>
            <textarea 
              v-model="journal.freeWrite"
              class="input-field resize-none"
              rows="4"
              placeholder="Tulis apapun yang ingin kamu ceritakan..."
            ></textarea>
          </div>
        </div>
      </div>
      
      <!-- Energy Level -->
      <div class="bg-white rounded-2xl p-6 shadow-sm border border-gray-100 mb-6">
        <h3 class="text-lg font-semibold text-gray-900 mb-4">Level Energi</h3>
        <div class="flex items-center space-x-4">
          <span class="text-sm text-gray-600">Rendah</span>
          <div class="flex-1">
            <input 
              v-model="journal.energyLevel"
              type="range" 
              min="1" 
              max="10" 
              class="w-full h-2 bg-gray-200 rounded-lg appearance-none cursor-pointer"
            />
            <div class="flex justify-between text-xs text-gray-500 mt-1">
              <span>1</span>
              <span>5</span>
              <span>10</span>
            </div>
          </div>
          <span class="text-sm text-gray-600">Tinggi</span>
        </div>
        <div class="text-center mt-2">
          <span class="text-lg font-semibold text-purple-600">{{ journal.energyLevel }}/10</span>
        </div>
      </div>
      
      <!-- Save Button -->
      <button 
        @click="saveJournal"
        :disabled="!canSave || saving"
        class="btn-primary w-full flex items-center justify-center mb-20"
        :class="!canSave ? 'opacity-50 cursor-not-allowed' : ''"
      >
        <LoaderIcon v-if="saving" size="20" class="mr-2 animate-spin" />
        <SaveIcon v-else size="20" class="mr-2" />
        {{ saving ? 'Menyimpan...' : 'Simpan Journal' }}
      </button>
    </div>
    
    <!-- Journal History Modal -->
    <div v-if="showHistory" class="fixed inset-0 bg-black/50 flex items-end z-50">
      <div class="bg-white w-full max-h-[80vh] rounded-t-3xl p-6 animate-slide-up">
        <div class="flex items-center justify-between mb-6">
          <h3 class="text-xl font-semibold text-gray-900">Riwayat Journal</h3>
          <button @click="showHistory = false" class="p-2 hover:bg-gray-100 rounded-full">
            <XIcon size="24" class="text-gray-600" />
          </button>
        </div>
        
        <div class="space-y-4 max-h-96 overflow-y-auto">
          <div 
            v-for="entry in journalHistory" 
            :key="entry.id"
            class="bg-gray-50 rounded-xl p-4 hover:bg-gray-100 transition-colors duration-200 cursor-pointer"
            @click="viewJournalEntry(entry)"
          >
            <div class="flex items-center justify-between mb-2">
              <div class="flex items-center">
                <span class="text-2xl mr-3">{{ getMoodEmoji(entry.mood) }}</span>
                <div>
                  <div class="font-medium text-gray-900">{{ entry.date }}</div>
                  <div class="text-sm text-gray-600">{{ entry.title || 'Journal Entry' }}</div>
                </div>
              </div>
              <ChevronRightIcon size="16" class="text-gray-400" />
            </div>
            <p class="text-sm text-gray-700 line-clamp-2">{{ entry.preview }}</p>
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

// Meta untuk halaman journal
useHead({
  title: 'Daily Journal - RealMe',
  meta: [
    { name: 'description', content: 'Tulis jurnal harian untuk refleksi dan meningkatkan self-acceptance. Catat perasaan dan pencapaianmu setiap hari.' }
  ]
})

// Data
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

const journalStreak = ref(5)
const showHistory = ref(false)
const saving = ref(false)

// Mood options
const detailedMoods = [
  { id: 1, emoji: '😊', label: 'Bahagia' },
  { id: 2, emoji: '😌', label: 'Tenang' },
  { id: 3, emoji: '😔', label: 'Sedih' },
  { id: 4, emoji: '😰', label: 'Cemas' },
  { id: 5, emoji: '😡', label: 'Marah' },
  { id: 6, emoji: '😴', label: 'Lelah' },
  { id: 7, emoji: '🤔', label: 'Bingung' },
  { id: 8, emoji: '😎', label: 'Percaya Diri' }
]

const selectedMood = ref(null)

// Journal form
const journal = reactive({
  gratitude: '',
  achievement: '',
  selfLove: '',
  freeWrite: '',
  energyLevel: 5
})

// Sample journal history
const journalHistory = ref([
  {
    id: 1,
    date: '29 Juli 2025',
    mood: 1,
    title: 'Hari yang Produktif',
    preview: 'Hari ini aku berhasil menyelesaikan tugas kuliah dan merasa senang...'
  },
  {
    id: 2,
    date: '28 Juli 2025',
    mood: 3,
    title: 'Merasa Sedikit Down',
    preview: 'Sempat merasa sedih karena komentar teman, tapi aku coba untuk tidak terlalu memikirkannya...'
  },
  {
    id: 3,
    date: '27 Juli 2025',
    mood: 2,
    title: 'Self-Care Day',
    preview: 'Menghabiskan waktu untuk merawat diri sendiri, mandi lama, dan nonton film favorit...'
  }
])

// Computed
const canSave = computed(() => {
  return selectedMood.value && (
    journal.gratitude.trim() || 
    journal.achievement.trim() || 
    journal.selfLove.trim() || 
    journal.freeWrite.trim()
  )
})

// Methods
const selectMood = (moodId) => {
  selectedMood.value = moodId
}

const saveJournal = async () => {
  if (!canSave.value) return
  
  saving.value = true
  
  try {
    // Simulasi API call
    await new Promise(resolve => setTimeout(resolve, 2000))
    
    // Reset form
    selectedMood.value = null
    Object.keys(journal).forEach(key => {
      if (key === 'energyLevel') {
        journal[key] = 5
      } else {
        journal[key] = ''
      }
    })
    
    // Update streak
    journalStreak.value++
    
    alert('Journal berhasil disimpan! +15 XP')
  } catch (error) {
    console.error('Error saving journal:', error)
    alert('Terjadi kesalahan saat menyimpan journal.')
  } finally {
    saving.value = false
  }
}

const getMoodEmoji = (moodId) => {
  const mood = detailedMoods.find(m => m.id === moodId)
  return mood ? mood.emoji : '😊'
}

const viewJournalEntry = (entry) => {
  // Navigate to journal detail page
  console.log('View journal entry:', entry)
  showHistory.value = false
}
</script>

<style scoped>
.line-clamp-2 {
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

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
}</style>
