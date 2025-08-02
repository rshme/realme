<template>
  <div class="min-h-screen bg-gray-50">
    <!-- Header -->
    <div class="safe-area-top bg-white shadow-sm">
      <div class="flex items-center justify-between p-4">
        <button @click="$router.back()" class="p-2 hover:bg-gray-100 rounded-full">
          <ArrowLeftIcon size="24" class="text-gray-600" />
        </button>
        <h1 class="text-xl font-semibold text-gray-900">Daily Journal</h1>
        <div class="flex space-x-1">
          <button 
            @click="activeTab = 'write'"
            class="p-2 rounded-full transition-colors duration-200"
            :class="activeTab === 'write' ? 'bg-purple-100 text-purple-600' : 'text-gray-600 hover:bg-gray-100'"
          >
            <EditIcon size="20" />
          </button>
          <button 
            @click="activeTab = 'history'"
            class="p-2 rounded-full transition-colors duration-200"
            :class="activeTab === 'history' ? 'bg-purple-100 text-purple-600' : 'text-gray-600 hover:bg-gray-100'"
          >
            <CalendarIcon size="20" />
          </button>
        </div>
      </div>
    </div>
    
    <!-- Content Area -->
    <div class="p-6">
      <!-- Write Journal Tab -->
      <div v-if="activeTab === 'write'">
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
              <label for="gratitude" class="block text-sm font-medium text-gray-700 mb-2">
                Apa yang membuatmu bersyukur hari ini?
              </label>
              <textarea 
                id="gratitude"
                v-model="journal.gratitude"
                class="input-field resize-none"
                rows="3"
                placeholder="Tulis minimal 1 hal yang membuatmu bersyukur..."
              ></textarea>
            </div>
            
            <div>
              <label for="achievement" class="block text-sm font-medium text-gray-700 mb-2">
                Pencapaian apa yang kamu raih hari ini? (sekecil apapun)
              </label>
              <textarea 
                id="achievement"
                v-model="journal.achievement"
                class="input-field resize-none"
                rows="3"
                placeholder="Misal: Makan teratur, menyelesaikan tugas, membantu teman..."
              ></textarea>
            </div>
            
            <div>
              <label for="selfLove" class="block text-sm font-medium text-gray-700 mb-2">
                Apa yang kamu sukai dari dirimu hari ini?
              </label>
              <textarea 
                id="selfLove"
                v-model="journal.selfLove"
                class="input-field resize-none"
                rows="3"
                placeholder="Fokus pada hal positif tentang dirimu..."
              ></textarea>
            </div>
            
            <div>
              <label for="freeWrite" class="block text-sm font-medium text-gray-700 mb-2">
                Cerita bebas tentang harimu
              </label>
              <textarea 
                id="freeWrite"
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

      <!-- History Tab -->
      <div v-if="activeTab === 'history'">
        <!-- Mood Chart -->
        <div class="bg-white rounded-2xl p-6 shadow-sm border border-gray-100 mb-6">
          <h3 class="text-lg font-semibold text-gray-900 mb-4">Mood Tracker - 7 Hari Terakhir</h3>
          
          <!-- Bar Chart Container -->
          <div class="relative h-48 mb-6">
            <!-- Y-axis labels -->
            <div class="absolute left-0 top-0 h-full flex flex-col justify-between text-xs text-gray-400 pr-3">
              <span>😊 10</span>
              <span>😐 5</span>
              <span>😢 1</span>
            </div>
            
            <!-- Chart area -->
            <div class="py-1 ml-8 h-full flex items-end justify-between space-x-2">
              <div 
                v-for="(mood, index) in moodData" 
                :key="index"
                class="flex-1 flex flex-col justify-end items-center group cursor-pointer h-full"
                @click="showDayDetail(weekDays[index].full, mood)"
              >
                <!-- Bar -->
                <div 
                  class="w-full relative rounded-t-lg transition-all duration-300 group-hover:scale-105"
                  :style="`height: ${(mood / 10) * 100}%; background: linear-gradient(to top, ${getMoodColor(mood)}, ${getMoodColorLight(mood)})`"
                >
                  <!-- Mood value tooltip -->
                  <div class="absolute -top-8 left-1/2 transform -translate-x-1/2 bg-gray-800 text-white text-xs px-2 py-1 rounded opacity-0 group-hover:opacity-100 transition-opacity duration-200">
                    {{ mood }}/10
                  </div>
                  
                  <!-- Mood emoji on bar -->
                  <div class="absolute -top-1 left-1/2 transform -translate-x-1/2 text-lg">
                    {{ getMoodEmojiByValue(mood) }}
                  </div>
                </div>
                
                <!-- Day label -->
                <div class="mt-3 text-xs font-medium text-gray-600">
                  {{ weekDays[index].short }}
                </div>
              </div>
            </div>
            
            <!-- Grid lines -->
            <div class="absolute inset-0 ml-8 pointer-events-none">
              <div class="h-full flex flex-col justify-between">
                <div class="border-t border-gray-100"></div>
                <div class="border-t border-gray-100"></div>
                <div class="border-t border-gray-100"></div>
                <div class="border-t border-gray-200"></div>
              </div>
            </div>
          </div>
          
          <!-- Mood Scale Legend -->
          <div class="flex justify-center items-center space-x-6 text-xs text-gray-500">
            <div class="flex items-center space-x-1">
              <div class="w-3 h-3 rounded" style="background: linear-gradient(to top, #ef4444, #fca5a5)"></div>
              <span>Sedih (1-3)</span>
            </div>
            <div class="flex items-center space-x-1">
              <div class="w-3 h-3 rounded" style="background: linear-gradient(to top, #f59e0b, #fde047)"></div>
              <span>Netral (4-6)</span>
            </div>
            <div class="flex items-center space-x-1">
              <div class="w-3 h-3 rounded" style="background: linear-gradient(to top, #10b981, #86efac)"></div>
              <span>Bahagia (7-10)</span>
            </div>
          </div>
        </div>

        <!-- Weekly Summary -->
        <div class="bg-gradient-to-r from-purple-500 to-blue-600 rounded-2xl p-6 text-white mb-6">
          <h3 class="text-lg font-semibold mb-2">Ringkasan Minggu Ini</h3>
          <div class="grid grid-cols-2 gap-4">
            <div class="text-center">
              <div class="text-2xl font-bold">{{ averageMood.toFixed(1) }}</div>
              <div class="text-sm text-white/80">Rata-rata Mood</div>
            </div>
            <div class="text-center">
              <div class="text-2xl font-bold">{{ journalEntriesThisWeek }}</div>
              <div class="text-sm text-white/80">Jurnal Minggu Ini</div>
            </div>
          </div>
        </div>
        
        <!-- Journal History -->
        <div class="space-y-4 mb-20">
          <h3 class="text-lg font-semibold text-gray-900">Riwayat Jurnal</h3>
          <div 
            v-for="entry in journalHistory" 
            :key="entry.id"
            class="bg-white rounded-2xl p-5 shadow-sm border border-gray-100 hover:shadow-md transition-all duration-200 cursor-pointer"
            @click="viewJournalEntry(entry)"
          >
            <div class="flex items-start justify-between mb-3">
              <div class="flex items-center">
                <span class="text-3xl mr-4">{{ getMoodEmoji(entry.mood) }}</span>
                <div>
                  <div class="font-semibold text-gray-900 text-lg">{{ entry.date }}</div>
                  <div class="text-sm text-gray-600">{{ entry.title || 'Journal Entry' }}</div>
                </div>
              </div>
              <div class="flex items-center text-gray-400">
                <span class="text-xs mr-2">Baca selengkapnya</span>
                <ChevronRightIcon size="16" />
              </div>
            </div>
            <p class="text-gray-700 line-clamp-3 text-sm leading-relaxed">{{ entry.preview }}</p>
            
            <!-- Tags or highlights -->
            <div class="flex flex-wrap gap-2 mt-3">
              <span v-if="entry.hasGratitude" class="px-2 py-1 bg-green-100 text-green-700 text-xs rounded-full">
                🙏 Gratitude
              </span>
              <span v-if="entry.hasAchievement" class="px-2 py-1 bg-blue-100 text-blue-700 text-xs rounded-full">
                🏆 Achievement
              </span>
              <span v-if="entry.hasSelfLove" class="px-2 py-1 bg-pink-100 text-pink-700 text-xs rounded-full">
                💝 Self-Love
              </span>
            </div>
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
const activeTab = ref('write')
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

// Chart data
const weekDays = [
  { short: 'Sen', full: 'Senin' },
  { short: 'Sel', full: 'Selasa' },
  { short: 'Rab', full: 'Rabu' },
  { short: 'Kam', full: 'Kamis' },
  { short: 'Jum', full: 'Jumat' },
  { short: 'Sab', full: 'Sabtu' },
  { short: 'Min', full: 'Minggu' }
]

// Sample mood data for the last 7 days (scale 1-10, where 10 is happiest)
const moodData = ref([6, 8, 5, 7, 9, 6, 8])

// Sample journal history with additional properties
const journalHistory = ref([
  {
    id: 1,
    date: '29 Juli 2025',
    mood: 1,
    title: 'Hari yang Produktif',
    preview: 'Hari ini aku berhasil menyelesaikan tugas kuliah dan merasa senang karena bisa mengelola waktu dengan baik. Sempat video call dengan keluarga juga yang membuat mood semakin bagus.',
    hasGratitude: true,
    hasAchievement: true,
    hasSelfLove: false
  },
  {
    id: 2,
    date: '28 Juli 2025',
    mood: 3,
    title: 'Merasa Sedikit Down',
    preview: 'Sempat merasa sedih karena komentar teman, tapi aku coba untuk tidak terlalu memikirkannya. Menghabiskan waktu dengan mendengarkan musik dan menulis membantu menenangkan pikiran.',
    hasGratitude: true,
    hasAchievement: false,
    hasSelfLove: true
  },
  {
    id: 3,
    date: '27 Juli 2025',
    mood: 2,
    title: 'Self-Care Day',
    preview: 'Menghabiskan waktu untuk merawat diri sendiri, mandi lama, dan nonton film favorit. Merasa bersyukur punya waktu untuk istirahat dan melakukan hal-hal yang aku suka.',
    hasGratitude: true,
    hasAchievement: false,
    hasSelfLove: true
  },
  {
    id: 4,
    date: '26 Juli 2025',
    mood: 8,
    title: 'Moment Bersama Teman',
    preview: 'Hari ini jalan-jalan dengan teman-teman. Senang banget bisa ketawa lepas dan sharing cerita. Merasa beruntung punya teman-teman yang supportif.',
    hasGratitude: true,
    hasAchievement: false,
    hasSelfLove: false
  },
  {
    id: 5,
    date: '25 Juli 2025',
    mood: 6,
    title: 'Hari Biasa',
    preview: 'Hari yang cukup standar, tidak ada yang istimewa tapi juga tidak buruk. Menyelesaikan rutinitas harian dan merasa cukup puas dengan pencapaian kecil.',
    hasGratitude: false,
    hasAchievement: true,
    hasSelfLove: false
  }
])

// Computed properties for analytics
const averageMood = computed(() => {
  return moodData.value.reduce((sum, mood) => sum + mood, 0) / moodData.value.length
})

const journalEntriesThisWeek = computed(() => {
  return journalHistory.value.length
})

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

const getMoodEmojiByValue = (value) => {
  if (value <= 3) return '😢'
  if (value <= 6) return '😐'
  return '😊'
}

const getMoodColor = (value) => {
  if (value <= 3) return '#ef4444' // Red for sad
  if (value <= 6) return '#f59e0b' // Orange for neutral
  return '#10b981' // Green for happy
}

const getMoodColorLight = (value) => {
  if (value <= 3) return '#fca5a5' // Light red
  if (value <= 6) return '#fde047' // Light yellow
  return '#86efac' // Light green
}

const viewJournalEntry = (entry) => {
  // Navigate to journal detail page
  console.log('View journal entry:', entry)
}

const showDayDetail = (day, mood) => {
  // Show detail for specific day with mood value
  console.log('Show detail for:', day, 'with mood:', mood)
}
</script>

<style scoped>
.line-clamp-2 {
  display: -webkit-box;
  -webkit-line-clamp: 2;
  line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

.line-clamp-3 {
  display: -webkit-box;
  -webkit-line-clamp: 3;
  line-clamp: 3;
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
}

/* Custom scrollbar for chart area */
.overflow-y-auto::-webkit-scrollbar {
  width: 4px;
}

.overflow-y-auto::-webkit-scrollbar-track {
  background: #f1f5f9;
  border-radius: 2px;
}

.overflow-y-auto::-webkit-scrollbar-thumb {
  background: #cbd5e1;
  border-radius: 2px;
}

.overflow-y-auto::-webkit-scrollbar-thumb:hover {
  background: #94a3b8;
}
</style>
