<template>
  <div class="min-h-screen bg-gray-50">
    <!-- Header -->
    <div class="safe-area-top bg-white shadow-sm">
      <div class="flex items-center justify-between p-4">
        <button
          @click="$router.back()"
          class="p-2 hover:bg-gray-100 rounded-full"
        >
          <ArrowLeftIcon size="24" class="text-gray-600" />
        </button>
        <h1 class="text-xl font-semibold text-gray-900">Mood Tracker</h1>
        <button
          @click="showHistory = true"
          class=""
        >
          <BarChart3Icon size="24" class="text-gray-600" />
        </button>
      </div>
    </div>

    <!-- Today's Date -->
    <div class="px-6 py-4">
      <div class="text-center">
        <h2 class="text-2xl font-bold text-gray-900">{{ currentDate }}</h2>
        <p class="text-gray-600">Bagaimana perasaanmu hari ini?</p>
      </div>
    </div>

    <!-- Mood Selection -->
    <div class="px-6 mb-6">
      <div class="bg-white rounded-2xl p-6 shadow-sm border border-gray-100">
        <h3 class="text-lg font-semibold text-gray-900 mb-4 text-center">
          Pilih Mood Utama
        </h3>
        <div class="grid grid-cols-3 gap-4 mb-6">
          <button
            v-for="mood in mainMoods"
            :key="mood.id"
            @click="
              showDetailModal = true;
              selectMood(mood.id);
            "
            class="flex flex-col items-center p-4 rounded-2xl transition-all duration-300 transform hover:scale-105"
            :class="
              selectedMood === mood.id
                ? 'bg-gradient-to-br from-purple-500 to-pink-500 text-white shadow-lg'
                : 'bg-gray-50 hover:bg-gray-100'
            "
          >
            <span
              class="text-4xl mb-2"
              :class="selectedMood === mood.id ? 'animate-bounce' : ''"
              >{{ mood.emoji }}</span
            >
            <span class="text-sm font-medium">{{ mood.label }}</span>
          </button>
        </div>
      </div>
    </div>

    <!-- Detail Mood Tracker Modal -->
    <div
      v-if="showDetailModal"
      class="fixed inset-0 bg-black/50 flex items-end z-50"
    >
      <div
        class="bg-white w-full max-h-[90vh] rounded-t-3xl animate-slide-up overflow-y-auto"
      >
        <!-- Modal Header -->
        <div class="sticky top-0 bg-white border-b border-gray-100 p-6 z-10">
          <div class="flex items-center justify-between">
            <div class="flex items-center">
              <span class="text-3xl mr-3">{{
                getMoodEmoji(selectedMood)
              }}</span>
              <div>
                <h3 class="text-xl font-semibold text-gray-900">
                  {{ getMoodLabel(selectedMood) }}
                </h3>
                <p class="text-sm text-gray-600">Lengkapi detail mood-mu</p>
              </div>
            </div>
            <button
              @click="showDetailModal = false"
              class="p-2 hover:bg-gray-100 rounded-full transition-colors duration-200"
            >
              <XIcon size="24" class="text-gray-600" />
            </button>
          </div>
        </div>

        <!-- Modal Content -->
        <div class="p-6 pb-8">
          <!-- Mood Intensity -->
          <div class="mb-6">
            <div
              class="bg-white rounded-2xl p-6 shadow-sm border border-gray-100"
            >
              <h4 class="text-lg font-semibold text-gray-900 mb-4">
                Intensitas Mood
              </h4>
              <div>
                <label
                  for="modal-mood-intensity"
                  class="block text-sm font-medium text-gray-700 mb-2"
                >
                  Seberapa kuat perasaan ini? ({{ moodIntensity }}/10)
                </label>
                <div class="relative">
                  <input
                    id="modal-mood-intensity"
                    v-model="moodIntensity"
                    type="range"
                    min="1"
                    max="10"
                    class="w-full h-3 bg-gray-200 rounded-lg appearance-none cursor-pointer mood-slider"
                  />
                  <div class="flex justify-between text-xs text-gray-500 mt-2">
                    <span>Lemah</span>
                    <span>Sedang</span>
                    <span>Kuat</span>
                  </div>
                </div>
              </div>
            </div>
          </div>

          <!-- Additional Emotions -->
          <div class="mb-6">
            <div
              class="bg-white rounded-2xl p-6 shadow-sm border border-gray-100"
            >
              <h4 class="text-lg font-semibold text-gray-900 mb-4">
                Emosi Tambahan
              </h4>
              <p class="text-sm text-gray-600 mb-4">
                Pilih emosi lain yang kamu rasakan (opsional)
              </p>
              <div class="flex flex-wrap gap-2">
                <button
                  v-for="emotion in additionalEmotions"
                  :key="emotion.id"
                  @click="toggleAdditionalEmotion(emotion.id)"
                  class="px-3 py-2 rounded-full text-sm font-medium transition-colors duration-200"
                  :class="
                    selectedAdditionalEmotions.includes(emotion.id)
                      ? 'bg-purple-100 text-purple-700 border-2 border-purple-300'
                      : 'bg-gray-100 text-gray-700 hover:bg-gray-200'
                  "
                >
                  {{ emotion.emoji }} {{ emotion.label }}
                </button>
              </div>
            </div>
          </div>

          <!-- Mood Triggers -->
          <div class="mb-6">
            <div
              class="bg-white rounded-2xl p-6 shadow-sm border border-gray-100"
            >
              <h4 class="text-lg font-semibold text-gray-900 mb-4">
                Apa yang Mempengaruhi?
              </h4>
              <p class="text-sm text-gray-600 mb-4">
                Pilih faktor yang mempengaruhi mood-mu
              </p>
              <div class="grid grid-cols-2 gap-3">
                <button
                  v-for="trigger in moodTriggers"
                  :key="trigger.id"
                  @click="toggleTrigger(trigger.id)"
                  class="flex items-center p-3 rounded-xl text-left transition-colors duration-200"
                  :class="
                    selectedTriggers.includes(trigger.id)
                      ? 'bg-blue-50 border-2 border-blue-200 text-blue-700'
                      : 'bg-gray-50 hover:bg-gray-100 text-gray-700'
                  "
                >
                  <span class="text-lg mr-3">{{ trigger.icon }}</span>
                  <span class="text-sm font-medium">{{ trigger.label }}</span>
                </button>
              </div>
            </div>
          </div>

          <!-- Notes -->
          <div class="mb-6">
            <div
              class="bg-white rounded-2xl p-6 shadow-sm border border-gray-100"
            >
              <h4 class="text-lg font-semibold text-gray-900 mb-4">
                Catatan (Opsional)
              </h4>
              <textarea
                v-model="moodNotes"
                class="w-full p-4 border border-gray-200 rounded-xl resize-none focus:ring-2 focus:ring-purple-500 focus:border-transparent"
                rows="4"
                placeholder="Ceritakan lebih detail tentang perasaanmu hari ini..."
              ></textarea>
            </div>
          </div>

          <!-- Action Buttons -->
          <div class="flex gap-3">
            <button
              @click="showDetailModal = false"
              class="flex-1 py-3 px-4 bg-gray-100 text-gray-700 font-semibold rounded-xl hover:bg-gray-200 transition-colors duration-200"
            >
              Kembali
            </button>
            <button
              @click="saveMoodEntry"
              class="flex-2 py-3 px-6 bg-gradient-to-r from-purple-600 to-pink-600 text-white font-semibold rounded-xl shadow-lg hover:shadow-xl transition-all duration-300 transform hover:scale-105"
            >
              <CheckCircleIcon size="18" class="inline mr-2" />
              Simpan Mood
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- Mood History -->
    <div class="w-full rounded-t-3xl p-6">
      <div class="flex items-center justify-between mb-6">
        <h3 class="text-xl font-semibold text-gray-900">Riwayat Mood</h3>
      </div>

      <!-- Mood Calendar -->
      <div class="mb-6">
        <h4 class="font-semibold text-gray-900 mb-3">7 Hari Terakhir</h4>
        <div class="grid grid-cols-7 gap-2">
          <div
            v-for="day in last7Days"
            :key="day.date"
            class="text-center p-3 rounded-xl"
            :class="day.mood ? getMoodBgColor(day.mood) : 'bg-gray-100'"
          >
            <div class="text-xs text-gray-600 mb-1">{{ day.dayName }}</div>
            <div class="text-lg">
              {{ day.mood ? getMoodEmoji(day.mood) : "😊" }}
            </div>
            <div class="text-xs font-medium">{{ day.date }}</div>
          </div>
        </div>
      </div>

      <!-- Mood Stats -->
      <div class="grid grid-cols-2 gap-4 mb-6">
        <div class="bg-purple-50 rounded-xl p-4 text-center">
          <div class="text-2xl font-bold text-purple-600">
            {{ moodStats.streak }}
          </div>
          <div class="text-sm text-purple-700">Hari Tracking</div>
        </div>
        <div class="bg-pink-50 rounded-xl p-4 text-center">
          <div class="text-2xl font-bold text-pink-600">
            {{ moodStats.averageIntensity }}
          </div>
          <div class="text-sm text-pink-700">Rata-rata Intensitas</div>
        </div>
      </div>

      <!-- Recent Entries -->
      <div class="pb-20">
        <h4 class="font-semibold text-gray-900 mb-3">Mood Terbaru</h4>
        <div class="space-y-3">
          <div
            v-for="entry in recentMoodEntries"
            :key="entry.id"
            class="bg-white shadow-sm border border-gray-100 rounded-xl p-4"
          >
            <div class="flex items-center justify-between mb-2">
              <div class="flex items-center">
                <span class="text-2xl mr-3">{{
                  getMoodEmoji(entry.mood)
                }}</span>
                <div>
                  <div class="font-medium text-gray-900">
                    {{ getMoodLabel(entry.mood) }}
                  </div>
                  <div class="text-sm text-gray-600">
                    Intensitas: {{ entry.intensity }}/10
                  </div>
                </div>
              </div>
              <div class="text-sm text-gray-500">{{ entry.date }}</div>
            </div>
            <p v-if="entry.notes" class="text-sm text-gray-700">
              {{ entry.notes }}
            </p>
          </div>
        </div>
      </div>
    </div>

    <!-- Bottom Navigation -->
    <BottomNavigation />
  </div>
</template>

<script setup>
import { ref, reactive, computed } from "vue";

// Meta untuk halaman mood tracker
useHead({
  title: "Mood Tracker - RealMe",
  meta: [
    {
      name: "description",
      content:
        "Pantau dan catat suasana hati harianmu. Pahami pola emosi dan faktor yang mempengaruhi mood.",
    },
  ],
});

const showDetailModal = ref(false);
const selectedMood = ref(null);
const moodIntensity = ref(5);
const selectedAdditionalEmotions = ref([]);
const selectedTriggers = ref([]);
const moodNotes = ref("");

// Current date
const currentDate = computed(() => {
  const today = new Date();
  const options = {
    weekday: "long",
    year: "numeric",
    month: "long",
    day: "numeric",
  };
  return today.toLocaleDateString("id-ID", options);
});

// Main moods
const mainMoods = [
  { id: 1, emoji: "😊", label: "Bahagia", color: "yellow" },
  { id: 2, emoji: "😌", label: "Tenang", color: "green" },
  { id: 3, emoji: "😐", label: "Netral", color: "gray" },
  { id: 4, emoji: "😔", label: "Sedih", color: "blue" },
  { id: 5, emoji: "😰", label: "Cemas", color: "orange" },
  { id: 6, emoji: "😡", label: "Marah", color: "red" },
];

// Additional emotions
const additionalEmotions = [
  { id: 1, emoji: "😴", label: "Lelah" },
  { id: 2, emoji: "🤗", label: "Penuh Kasih" },
  { id: 3, emoji: "😤", label: "Frustrasi" },
  { id: 4, emoji: "🥳", label: "Excited" },
  { id: 5, emoji: "😪", label: "Bosan" },
  { id: 6, emoji: "🤔", label: "Bingung" },
  { id: 7, emoji: "😓", label: "Stress" },
  { id: 8, emoji: "😍", label: "Penuh Cinta" },
];

// Mood triggers
const moodTriggers = [
  { id: 1, icon: "👥", label: "Interaksi Sosial" },
  { id: 2, icon: "💼", label: "Pekerjaan/Sekolah" },
  { id: 3, icon: "👪", label: "Keluarga" },
  { id: 4, icon: "🌤️", label: "Cuaca" },
  { id: 5, icon: "💪", label: "Olahraga" },
  { id: 6, icon: "🍎", label: "Makanan" },
  { id: 7, icon: "😴", label: "Tidur" },
  { id: 8, icon: "📱", label: "Media Sosial" },
];

// Mock data for history
const recentMoodEntries = ref([
  {
    id: 1,
    mood: 1,
    intensity: 8,
    date: "29 Juli 2025",
    notes: "Hari yang produktif! Berhasil menyelesaikan banyak tugas.",
    triggers: [1, 2],
  },
  {
    id: 2,
    mood: 3,
    intensity: 5,
    date: "28 Juli 2025",
    notes: "Hari biasa-biasa saja, tidak ada yang spesial.",
    triggers: [4],
  },
  {
    id: 3,
    mood: 5,
    intensity: 7,
    date: "27 Juli 2025",
    notes: "Sedikit cemas karena deadline yang mendekat.",
    triggers: [2, 7],
  },
]);

// Last 7 days for calendar view
const last7Days = computed(() => {
  const days = [];
  for (let i = 6; i >= 0; i--) {
    const date = new Date();
    date.setDate(date.getDate() - i);

    days.push({
      date: date.getDate(),
      dayName: date.toLocaleDateString("id-ID", { weekday: "short" }),
      mood: i === 0 ? null : Math.floor(Math.random() * 6) + 1, // Random mood for demo
    });
  }
  return days;
});

// Mood statistics
const moodStats = computed(() => {
  return {
    streak: 15,
    averageIntensity: 6.2,
  };
});

// Methods
const selectMood = (moodId) => {
  selectedMood.value = moodId;
};

const toggleAdditionalEmotion = (emotionId) => {
  const index = selectedAdditionalEmotions.value.indexOf(emotionId);
  if (index > -1) {
    selectedAdditionalEmotions.value.splice(index, 1);
  } else {
    selectedAdditionalEmotions.value.push(emotionId);
  }
};

const toggleTrigger = (triggerId) => {
  const index = selectedTriggers.value.indexOf(triggerId);
  if (index > -1) {
    selectedTriggers.value.splice(index, 1);
  } else {
    selectedTriggers.value.push(triggerId);
  }
};

const saveMoodEntry = () => {
  const entry = {
    id: Date.now(),
    mood: selectedMood.value,
    intensity: moodIntensity.value,
    additionalEmotions: selectedAdditionalEmotions.value,
    triggers: selectedTriggers.value,
    notes: moodNotes.value,
    date: new Date().toLocaleDateString("id-ID"),
  };

  // Add to recent entries
  recentMoodEntries.value.unshift(entry);

  // Close modal
  showDetailModal.value = false;

  // Reset form
  selectedMood.value = null;
  moodIntensity.value = 5;
  selectedAdditionalEmotions.value = [];
  selectedTriggers.value = [];
  moodNotes.value = "";

  // Show success message
  alert("Mood berhasil disimpan! +5 XP");
};

const getMoodEmoji = (moodId) => {
  const mood = mainMoods.find((m) => m.id === moodId);
  return mood ? mood.emoji : "😊";
};

const getMoodLabel = (moodId) => {
  const mood = mainMoods.find((m) => m.id === moodId);
  return mood ? mood.label : "Unknown";
};

const getMoodBgColor = (moodId) => {
  const colorMap = {
    1: "bg-yellow-100", // Bahagia
    2: "bg-green-100", // Tenang
    3: "bg-gray-100", // Netral
    4: "bg-blue-100", // Sedih
    5: "bg-orange-100", // Cemas
    6: "bg-red-100", // Marah
  };
  return colorMap[moodId] || "bg-gray-100";
};
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

/* Custom slider styles */
.mood-slider {
  background: linear-gradient(to right, #ef4444 0%, #f59e0b 50%, #10b981 100%);
}

.mood-slider::-webkit-slider-thumb {
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

.mood-slider::-webkit-slider-thumb:hover {
  transform: scale(1.1);
  box-shadow: 0 3px 12px rgba(0, 0, 0, 0.3);
}

.mood-slider::-moz-range-thumb {
  height: 22px;
  width: 22px;
  border-radius: 50%;
  background: white;
  cursor: pointer;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.2);
  border: 3px solid #8b5cf6;
  transition: all 0.2s ease;
}

.mood-slider::-moz-range-thumb:hover {
  transform: scale(1.1);
  box-shadow: 0 3px 12px rgba(0, 0, 0, 0.3);
}

/* Modal backdrop blur effect */
.modal-backdrop {
  backdrop-filter: blur(4px);
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
  .grid-cols-3 {
    grid-template-columns: repeat(3, 1fr);
  }

  .grid-cols-2 {
    grid-template-columns: repeat(2, 1fr);
  }

  .flex-2 {
    flex: 2;
  }
}
</style>
