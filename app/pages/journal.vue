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
          
        </div>
      </div>
    </div>
    
    <!-- Content Area -->
    <div class="p-6">
      <!-- Write Journal Tab -->
      <div v-if="activeTab === 'write'">
        <!-- Date & Streak -->
        <div class="rounded-2xl px-6 text-black mb-6">
          <div class="flex items-center justify-between">
            <div>
              <h2 class="text-lg font-semibold">{{ currentDate }}</h2>
              <p class="text-black/80">Bagaimana harimu?</p>
            </div>
            <div class="text-center">
              <div class="text-2xl mb-1">🔥</div>
              <div class="text-sm">{{ journalStreak }} hari</div>
            </div>
          </div>
        </div>

        <!-- CTA Button to Open Journal Modal -->
        <div class="bg-gradient-to-br from-blue-500 to-purple-600 rounded-2xl p-8 text-white mb-6 text-center">
          <div class="mb-4">
            <div class="text-4xl mb-2">📝</div>
            <h3 class="text-xl font-bold mb-2">Saatnya Menulis Journal</h3>
            <p class="text-white/80 text-sm mb-6">Refleksikan harimu dan catat momen-momen berharga</p>
          </div>
          <button 
            @click="showJournalModal = true"
            class="bg-white text-purple-600 font-semibold px-8 py-3 rounded-xl shadow-lg hover:shadow-xl transition-all duration-300 transform hover:scale-105"
          >
            <PenToolIcon size="18" class="inline mr-2" />
            Ayo catat jurnalmu hari ini
          </button>
        </div>

        <!-- Journal Writing Modal -->
        <div v-if="showJournalModal" class="fixed inset-0 bg-black/50 flex items-end z-50">
          <div class="bg-white w-full max-h-[90vh] rounded-t-3xl animate-slide-up overflow-y-auto">
            <!-- Modal Header -->
            <div class="sticky top-0 bg-white border-b border-gray-100 p-6 z-10">
              <div class="flex items-center justify-between">
                <div class="flex items-center">
                  <div class="w-10 h-10 bg-purple-100 rounded-full flex items-center justify-center mr-3">
                    <BookOpenIcon size="20" class="text-purple-600" />
                  </div>
                  <div>
                    <h3 class="text-xl font-semibold text-gray-900">Daily Journal</h3>
                    <p class="text-sm text-gray-600">{{ currentDate }}</p>
                  </div>
                </div>
                <button 
                  @click="showJournalModal = false" 
                  class="p-2 hover:bg-gray-100 rounded-full transition-colors duration-200"
                >
                  <XIcon size="24" class="text-gray-600" />
                </button>
              </div>
            </div>

            <!-- Modal Content -->
            <div class="p-6 pb-8">
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
                    <label for="modal-gratitude" class="block text-sm font-medium text-gray-700 mb-2">
                      Apa yang membuatmu bersyukur hari ini?
                    </label>
                    <textarea 
                      id="modal-gratitude"
                      v-model="journal.gratitude"
                      class="input-field resize-none"
                      rows="3"
                      placeholder="Tulis minimal 1 hal yang membuatmu bersyukur..."
                    ></textarea>
                  </div>
                  
                  <div>
                    <label for="modal-achievement" class="block text-sm font-medium text-gray-700 mb-2">
                      Pencapaian apa yang kamu raih hari ini? (sekecil apapun)
                    </label>
                    <textarea 
                      id="modal-achievement"
                      v-model="journal.achievement"
                      class="input-field resize-none"
                      rows="3"
                      placeholder="Misal: Makan teratur, menyelesaikan tugas, membantu teman..."
                    ></textarea>
                  </div>
                  
                  <div>
                    <label for="modal-selfLove" class="block text-sm font-medium text-gray-700 mb-2">
                      Apa yang kamu sukai dari dirimu hari ini?
                    </label>
                    <textarea 
                      id="modal-selfLove"
                      v-model="journal.selfLove"
                      class="input-field resize-none"
                      rows="3"
                      placeholder="Fokus pada hal positif tentang dirimu..."
                    ></textarea>
                  </div>
                  
                  <div>
                    <label for="modal-freeWrite" class="block text-sm font-medium text-gray-700 mb-2">
                      Cerita bebas tentang harimu
                    </label>
                    <textarea 
                      id="modal-freeWrite"
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
                      class="w-full h-3 bg-gray-200 rounded-lg appearance-none cursor-pointer energy-slider"
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
              
              <!-- Action Buttons -->
              <div class="flex gap-3">
                <button 
                  @click="showJournalModal = false"
                  class="flex-1 py-3 px-4 bg-gray-100 text-gray-700 font-semibold rounded-xl hover:bg-gray-200 transition-colors duration-200"
                >
                  Tutup
                </button>
                <button 
                  @click="saveJournal"
                  :disabled="!canSave || saving"
                  class="flex-2 py-3 px-6 bg-gradient-to-r from-blue-600 to-purple-600 text-white font-semibold rounded-xl shadow-lg hover:shadow-xl transition-all duration-300 transform hover:scale-105"
                  :class="!canSave ? 'opacity-50 cursor-not-allowed transform-none' : ''"
                >
                  <LoaderIcon v-if="saving" size="18" class="inline mr-2 animate-spin" />
                  <SaveIcon v-else size="18" class="inline mr-2" />
                  {{ saving ? 'Menyimpan...' : 'Simpan Journal' }}
                </button>
              </div>
            </div>
          </div>
        </div>

        <!-- Weekly Summary -->
        <div class="bg-gradient-to-r from-purple-500 to-blue-600 rounded-2xl p-6 text-white mb-6">
          <h3 class="text-lg font-semibold mb-5">Ringkasan Minggu Ini</h3>
          <div class="grid grid-cols-2 gap-4">
            <div class="text-center">
              <div class="text-2xl font-bold mb-1.5">{{ averageMood.toFixed(1) }}</div>
              <div class="text-sm text-white/80">Rata-rata Level Energi</div>
            </div>
            <div class="text-center">
              <div class="text-2xl font-bold mb-1.5">{{ journalEntriesThisWeek }}</div>
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
const showJournalModal = ref(false)
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
    
    // Close modal
    showJournalModal.value = false
    
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

/* Energy slider styles */
.energy-slider {
  background: linear-gradient(to right, #ef4444 0%, #f59e0b 50%, #10b981 100%);
}

.energy-slider::-webkit-slider-thumb {
  appearance: none;
  height: 22px;
  width: 22px;
  border-radius: 50%;
  background: white;
  cursor: pointer;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.2);
  border: 3px solid #8b5cf6;
  transition: all 0.2s ease;
}

.energy-slider::-webkit-slider-thumb:hover {
  transform: scale(1.1);
  box-shadow: 0 3px 12px rgba(0, 0, 0, 0.3);
}

.energy-slider::-moz-range-thumb {
  height: 22px;
  width: 22px;
  border-radius: 50%;
  background: white;
  cursor: pointer;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.2);
  border: 3px solid #8b5cf6;
  transition: all 0.2s ease;
}

.energy-slider::-moz-range-thumb:hover {
  transform: scale(1.1);
  box-shadow: 0 3px 12px rgba(0, 0, 0, 0.3);
}

/* Custom scrollbar for modal */
.overflow-y-auto::-webkit-scrollbar {
  width: 6px;
}

.overflow-y-auto::-webkit-scrollbar-track {
  background: #f1f5f9;
  border-radius: 3px;
}

.overflow-y-auto::-webkit-scrollbar-thumb {
  background: #cbd5e1;
  border-radius: 3px;
}

.overflow-y-auto::-webkit-scrollbar-thumb:hover {
  background: #94a3b8;
}

/* Responsive adjustments */
@media (max-width: 640px) {
  .flex-2 {
    flex: 2;
  }
}
</style>
