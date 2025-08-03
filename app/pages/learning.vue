<template>
  <div class="min-h-screen bg-gray-50">
    <!-- Header -->
    <div class="safe-area-top bg-white shadow-sm">
      <div class="flex items-center justify-between p-4">
        <button @click="$router.back()" class="p-2 hover:bg-gray-100 rounded-full">
          <ArrowLeftIcon size="24" class="text-gray-600" />
        </button>
        <h1 class="text-xl font-semibold text-gray-900">Learning Hub</h1>
        <button @click="showProgress = true" class="p-2 hover:bg-gray-100 rounded-full">
          <BarChart2Icon size="24" class="text-gray-600" />
        </button>
      </div>
    </div>
    
    <!-- Progress Overview -->
    <div class="px-6 py-4">
      <div class="bg-gradient-to-r from-green-500 to-teal-600 rounded-2xl p-6 text-white">
        <div class="flex items-center justify-between mb-4">
          <div>
            <h2 class="text-lg font-semibold">Progress Belajar</h2>
            <p class="text-white/80">{{ completedModules }}/{{ totalModules }} modul selesai</p>
          </div>
          <div class="text-center">
            <div class="text-2xl font-bold">{{ Math.round((completedModules/totalModules) * 100) }}%</div>
            <div class="text-sm text-white/80">Selesai</div>
          </div>
        </div>
        <div class="w-full bg-white/20 rounded-full h-2">
          <div 
            class="bg-white h-2 rounded-full transition-all duration-300"
            :style="{ width: `${(completedModules/totalModules) * 100}%` }"
          ></div>
        </div>
      </div>
    </div>

    <!-- Tabs Section -->
    <div class="px-6 py-2 mb-5">
      <div class="flex space-x-1 bg-gray-100 rounded-xl p-1">
        <button 
          @click="activeTab = 'my-courses'"
          class="flex-1 py-3 px-4 rounded-lg font-medium text-sm transition-all duration-200"
          :class="activeTab === 'my-courses' ? 'bg-white text-purple-600 shadow-sm' : 'text-gray-600 hover:text-gray-900'"
        >
          <LayersIcon size="18" class="inline mr-2" />
          My Courses
        </button>
        <button 
          @click="activeTab = 'explore'"
          class="flex-1 py-3 px-4 rounded-lg font-medium text-sm transition-all duration-200"
          :class="activeTab === 'explore' ? 'bg-white text-purple-600 shadow-sm' : 'text-gray-600 hover:text-gray-900'"
        >
          <BookOpenIcon size="18" class="inline mr-2" />
          Explore
        </button>
      </div>
    </div>

    <!-- My Courses Tab Content -->
    <div v-if="activeTab === 'my-courses'" class="px-6 space-y-6 pb-24">
      <!-- My Favorites Section -->
      <div>
        <div class="flex items-center justify-between mb-4">
          <h3 class="text-lg font-semibold text-gray-900">My Favorites</h3>
          <span class="text-sm text-gray-500">{{ favoriteModules.length }} course{{ favoriteModules.length !== 1 ? 's' : '' }}</span>
        </div>
        
        <div v-if="favoriteModules.length === 0" class="text-center py-8">
          <StarIcon size="48" class="mx-auto text-gray-300 mb-4" />
          <p class="text-gray-500">Belum ada course favorit</p>
          <p class="text-sm text-gray-400">Tambahkan course ke favorit dengan menekan ikon bintang</p>
        </div>
        
        <div v-else class="space-y-4">
          <div 
            v-for="module in favoriteModules" 
            :key="`fav-${module.id}`"
            class="bg-white rounded-2xl shadow-sm border border-gray-100 overflow-hidden"
          >
            <div class="p-4">
              <div class="flex items-start justify-between">
                <div class="flex-1">
                  <div class="flex items-center mb-2">
                    <div 
                      class="w-8 h-8 rounded-full flex items-center justify-center mr-3"
                      :class="getModuleIconStyle(module.category)"
                    >
                      <HeartIcon v-if="module.category === 'self-acceptance'" size="16" class="text-white" />
                      <ZapIcon v-else-if="module.category === 'cbt'" size="16" class="text-white" />
                      <UserIcon v-else-if="module.category === 'body-image'" size="16" class="text-white" />
                      <SunIcon v-else-if="module.category === 'mindfulness'" size="16" class="text-white" />
                      <BookIcon v-else size="16" class="text-white" />
                    </div>
                    <div class="flex-1">
                      <h4 class="font-semibold text-gray-900 text-sm">{{ module.title }}</h4>
                      <p class="text-xs text-gray-600">{{ module.duration }} • {{ module.difficulty }}</p>

                      <!-- Progress Bar for favorites -->
                      <div v-if="module.inProgress || module.completed" class="mt-2">
                        <div class="w-full bg-gray-200 rounded-full h-1.5">
                          <div 
                            class="h-1.5 rounded-full transition-all duration-300"
                            :class="module.completed ? 'bg-green-500' : 'bg-yellow-500'"
                            :style="{ width: `${module.progress}%` }"
                          ></div>
                        </div>
                      </div>

                      <button 
                        @click="openModule(module)"
                        :disabled="module.locked"
                        class="mt-3 px-3 py-1.5 text-xs font-medium rounded-lg transition-colors duration-200"
                        :class="getButtonStyle(module)"
                      >
                        {{ getButtonText(module) }}
                      </button>
                    </div>
                  </div>
                </div>
                
                <div class="flex items-center space-x-2 ml-4">
                  <button 
                    @click="removeFavorite(module.id)"
                    class="p-1 text-yellow-500 hover:text-yellow-600"
                  >
                    <StarIcon size="20" class="fill-current" />
                  </button>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Recent Played Section -->
      <div>
        <div class="flex items-center justify-between mb-4">
          <h3 class="text-lg font-semibold text-gray-900">Recently Played</h3>
          <span class="text-sm text-gray-500">{{ recentModules.length }} course{{ recentModules.length !== 1 ? 's' : '' }}</span>
        </div>
        
        <div v-if="recentModules.length === 0" class="text-center py-8">
          <ClockIcon size="48" class="mx-auto text-gray-300 mb-4" />
          <p class="text-gray-500">Belum ada riwayat belajar</p>
          <p class="text-sm text-gray-400">Mulai belajar untuk melihat course yang baru saja kamu akses</p>
        </div>
        
        <div v-else class="space-y-4">
          <div 
            v-for="module in recentModules" 
            :key="`recent-${module.id}`"
            class="bg-white rounded-2xl shadow-sm border border-gray-100 overflow-hidden"
          >
            <div class="p-4">
              <div class="flex items-start justify-between">
                <div class="flex-1">
                  <div class="flex items-center mb-2">
                    <div 
                      class="w-8 h-8 rounded-full flex items-center justify-center mr-3"
                      :class="getModuleIconStyle(module.category)"
                    >
                      <HeartIcon v-if="module.category === 'self-acceptance'" size="16" class="text-white" />
                      <ZapIcon v-else-if="module.category === 'cbt'" size="16" class="text-white" />
                      <UserIcon v-else-if="module.category === 'body-image'" size="16" class="text-white" />
                      <SunIcon v-else-if="module.category === 'mindfulness'" size="16" class="text-white" />
                      <BookIcon v-else size="16" class="text-white" />
                    </div>
                    <div class="flex-1">
                      <h4 class="font-semibold text-gray-900 text-sm">{{ module.title }}</h4>
                      <p class="text-xs text-gray-600">{{ module.duration }} • {{ module.difficulty }}</p>
                      <p class="text-xs text-gray-500">{{ module.lastAccessed }}</p>

                      <!-- Progress Bar for recent -->
                      <div v-if="module.inProgress || module.completed" class="mt-2">
                        <div class="w-full bg-gray-200 rounded-full h-1.5">
                          <div 
                            class="h-1.5 rounded-full transition-all duration-300"
                            :class="module.completed ? 'bg-green-500' : 'bg-yellow-500'"
                            :style="{ width: `${module.progress}%` }"
                          ></div>
                        </div>
                      </div>

                      <button 
                        @click="openModule(module)"
                        :disabled="module.locked"
                        class="mt-3 px-3 py-1.5 text-xs font-medium rounded-lg transition-colors duration-200"
                        :class="getButtonStyle(module)"
                      >
                        {{ getButtonText(module) }}
                      </button>
                    </div>
                  </div>
                </div>
                
                <div class="flex items-center space-x-2 ml-4">
                  <button 
                    @click="toggleFavorite(module.id)"
                    class="p-1 transition-colors duration-200"
                    :class="isFavorite(module.id) ? 'text-yellow-500' : 'text-gray-400 hover:text-yellow-500'"
                  >
                    <StarIcon size="20" :class="isFavorite(module.id) ? 'fill-current' : ''" />
                  </button>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Explore Tab Content -->
    <div v-if="activeTab === 'explore'">
      <!-- Learning Categories -->
      <div class="px-6 mb-4">
        <h3 class="text-lg font-semibold text-gray-900 mb-3">Kategori Pembelajaran</h3>
        <div class="flex space-x-3 overflow-x-auto pb-2 scrollbar-hide">
          <button 
            v-for="category in categories" 
            :key="category.id"
            @click="activeCategory = category.id"
            class="flex-shrink-0 px-4 py-2 rounded-full text-sm font-medium transition-colors duration-200"
            :class="activeCategory === category.id ? 'bg-purple-600 text-white' : 'bg-white text-gray-700 hover:bg-purple-50'"
          >
            {{ category.name }}
          </button>
        </div>
      </div>
      
      <!-- Learning Modules -->
      <div class="px-6 space-y-4 mb-20 pb-6">
        <div 
          v-for="module in filteredModules" 
          :key="module.id"
          class="bg-white rounded-2xl shadow-sm border border-gray-100 overflow-hidden"
        >
          <div class="p-6">
            <!-- Module Header -->
            <div class="flex items-start justify-between mb-4">
              <div class="flex-1">
                <div class="flex items-center mb-2">
                  <div 
                    class="w-10 h-10 rounded-full flex items-center justify-center mr-3"
                    :class="getModuleIconStyle(module.category)"
                  >
                    <HeartIcon v-if="module.category === 'self-acceptance'" size="20" class="text-white" />
                    <ZapIcon v-else-if="module.category === 'cbt'" size="20" class="text-white" />
                    <UserIcon v-else-if="module.category === 'body-image'" size="20" class="text-white" />
                    <SunIcon v-else-if="module.category === 'mindfulness'" size="20" class="text-white" />
                    <BookIcon v-else size="20" class="text-white" />
                  </div>
                  <div>
                    <h4 class="font-semibold text-gray-900">{{ module.title }}</h4>
                    <p class="text-sm text-gray-600">{{ module.duration }} • {{ module.difficulty }}</p>
                  </div>
                </div>
                <p class="text-gray-700 text-sm mb-4">{{ module.description }}</p>
              </div>
              
              <div class="ml-4 flex items-center space-x-2">
                <!-- Favorite Star Button -->
                <button 
                  @click="toggleFavorite(module.id)"
                  class="p-2 transition-colors duration-200"
                  :class="isFavorite(module.id) ? 'text-yellow-500' : 'text-gray-400 hover:text-yellow-500'"
                >
                  <StarIcon size="20" :class="isFavorite(module.id) ? 'fill-current' : ''" />
                </button>
                
                <!-- Status Icon -->
                <div>
                  <div 
                    v-if="module.completed"
                    class="w-8 h-8 bg-green-100 rounded-full flex items-center justify-center"
                  >
                    <CheckIcon size="16" class="text-green-600" />
                  </div>
                  <div 
                    v-else-if="module.inProgress"
                    class="w-8 h-8 bg-yellow-100 rounded-full flex items-center justify-center"
                  >
                    <PlayIcon size="16" class="text-yellow-600" />
                  </div>
                  <div 
                    v-else
                    class="w-8 h-8 bg-gray-100 rounded-full flex items-center justify-center"
                  >
                    <LockIcon size="16" class="text-gray-400" />
                  </div>
                </div>
              </div>
            </div>
            
            <!-- Progress Bar -->
            <div v-if="module.inProgress || module.completed" class="mb-4">
              <div class="flex justify-between text-sm text-gray-600 mb-1">
                <span>Progress</span>
                <span>{{ module.progress }}%</span>
              </div>
              <div class="w-full bg-gray-200 rounded-full h-2">
                <div 
                  class="h-2 rounded-full transition-all duration-300"
                  :class="module.completed ? 'bg-green-500' : 'bg-yellow-500'"
                  :style="{ width: `${module.progress}%` }"
                ></div>
              </div>
            </div>
            
            <!-- Module Topics -->
            <div class="mb-4">
              <h5 class="font-medium text-gray-900 mb-2">Yang akan kamu pelajari:</h5>
              <ul class="space-y-1">
                <li 
                  v-for="topic in module.topics" 
                  :key="topic"
                  class="flex items-center text-sm text-gray-600"
                >
                  <CheckCircleIcon size="14" class="text-green-500 mr-2 flex-shrink-0" />
                  {{ topic }}
                </li>
              </ul>
            </div>
            
            <!-- Action Button -->
            <button 
              @click="openModule(module)"
              :disabled="module.locked"
              class="w-full py-3 px-4 rounded-xl font-medium transition-colors duration-200 flex items-center justify-center"
              :class="getButtonStyle(module)"
            >
              <LockIcon v-if="module.locked" size="16" class="mr-2" />
              <CheckCircleIcon v-else-if="module.completed" size="16" class="mr-2" />
              <PlayIcon v-else-if="module.inProgress" size="16" class="mr-2" />
              <PlayCircleIcon v-else size="16" class="mr-2" />
              {{ getButtonText(module) }}
            </button>
          </div>
        </div>
      </div>
    </div>
    
    <!-- Progress Detail Modal -->
    <div v-if="showProgress" class="fixed inset-0 bg-black/50 flex items-end z-50">
      <div class="bg-white w-full max-h-[70vh] rounded-t-3xl p-6 animate-slide-up">
        <div class="flex items-center justify-between mb-6">
          <h3 class="text-xl font-semibold text-gray-900">Detail Progress</h3>
          <button @click="showProgress = false" class="p-2 hover:bg-gray-100 rounded-full">
            <XIcon size="24" class="text-gray-600" />
          </button>
        </div>
        
        <div class="space-y-4">
          <!-- Overall Stats -->
          <div class="grid grid-cols-3 gap-4">
            <div class="text-center p-4 bg-green-50 rounded-xl">
              <div class="text-2xl font-bold text-green-600">{{ completedModules }}</div>
              <div class="text-sm text-green-700">Selesai</div>
            </div>
            <div class="text-center p-4 bg-yellow-50 rounded-xl">
              <div class="text-2xl font-bold text-yellow-600">{{ inProgressModules }}</div>
              <div class="text-sm text-yellow-700">Sedang Belajar</div>
            </div>
            <div class="text-center p-4 bg-purple-50 rounded-xl">
              <div class="text-2xl font-bold text-purple-600">{{ totalXPFromLearning }}</div>
              <div class="text-sm text-purple-700">XP Earned</div>
            </div>
          </div>
          
          <!-- Category Progress -->
          <div>
            <h4 class="font-semibold text-gray-900 mb-3">Progress per Kategori</h4>
            <div class="space-y-3">
              <div v-for="category in categories.filter(c => c.id !== 'all')" :key="category.id">
                <div class="flex justify-between text-sm text-gray-600 mb-1">
                  <span>{{ category.name }}</span>
                  <span>{{ getCategoryProgress(category.id) }}%</span>
                </div>
                <div class="w-full bg-gray-200 rounded-full h-2">
                  <div 
                    class="h-2 bg-purple-500 rounded-full transition-all duration-300"
                    :style="{ width: `${getCategoryProgress(category.id)}%` }"
                  ></div>
                </div>
              </div>
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
import { ref, computed } from 'vue'

// Meta untuk halaman learning
useHead({
  title: 'Learning Hub - RealMe',
  meta: [
    { name: 'description', content: 'Pelajari teknik CBT dan self-acceptance melalui modul pembelajaran yang mudah dipahami dan interaktif.' }
  ]
})

const showProgress = ref(false)
const activeCategory = ref('all')
const activeTab = ref('my-courses')

// Favorites and recent data
const favoriteIds = ref([1]) // Sample: first module is already favorited
const recentAccess = ref([
  { id: 1, lastAccessed: '2 jam lalu' },
  { id: 2, lastAccessed: '1 hari lalu' }
])

// Categories
const categories = [
  { id: 'all', name: 'Semua' },
  { id: 'self-acceptance', name: 'Self-Acceptance' },
  { id: 'cbt', name: 'CBT Basics' },
  { id: 'body-image', name: 'Body Image' },
  { id: 'mindfulness', name: 'Mindfulness' }
]

// Learning modules
const modules = ref([
  {
    id: 1,
    title: 'Pengenalan Self-Acceptance',
    description: 'Memahami konsep dasar self-acceptance dan mengapa hal ini penting untuk kesehatan mental.',
    category: 'self-acceptance',
    duration: '15 menit',
    difficulty: 'Pemula',
    completed: true,
    inProgress: false,
    locked: false,
    progress: 100,
    topics: [
      'Apa itu self-acceptance?',
      'Perbedaan self-acceptance dan self-esteem',
      'Manfaat menerima diri sendiri',
      'Mitos tentang self-acceptance'
    ],
    xpReward: 20
  },
  {
    id: 2,
    title: 'Mengenali Negative Self-Talk',
    description: 'Belajar mengidentifikasi dan mengubah pola pikir negatif yang merusak self-acceptance.',
    category: 'cbt',
    duration: '20 menit',
    difficulty: 'Pemula',
    completed: false,
    inProgress: true,
    locked: false,
    progress: 60,
    topics: [
      'Jenis-jenis negative self-talk',
      'Dampak negative self-talk',
      'Teknik mengenali pola pikir negatif',
      'Latihan mindfulness untuk awareness'
    ],
    xpReward: 25
  },
  {
    id: 3,
    title: 'Body Neutrality vs Body Positivity',
    description: 'Memahami konsep body neutrality sebagai alternatif yang lebih realistis dari body positivity.',
    category: 'body-image',
    duration: '18 menit',
    difficulty: 'Menengah',
    completed: false,
    inProgress: false,
    locked: false,
    progress: 0,
    topics: [
      'Perbedaan body positivity dan body neutrality',
      'Mengapa body neutrality lebih sustainable',
      'Latihan menerima tubuh apa adanya',
      'Fokus pada fungsi, bukan penampilan'
    ],
    xpReward: 30
  },
  {
    id: 4,
    title: 'Mindful Self-Compassion',
    description: 'Mengembangkan sikap compassionate terhadap diri sendiri melalui praktik mindfulness.',
    category: 'mindfulness',
    duration: '25 menit',
    difficulty: 'Menengah',
    completed: false,
    inProgress: false,
    locked: true,
    progress: 0,
    topics: [
      'Konsep self-compassion',
      'Tiga komponen self-compassion',
      'Guided meditation for self-compassion',
      'Self-compassion dalam keseharian'
    ],
    xpReward: 35
  },
  {
    id: 5,
    title: 'Cognitive Restructuring',
    description: 'Teknik CBT untuk mengubah pola pikir negatif menjadi lebih realistis dan seimbang.',
    category: 'cbt',
    duration: '30 menit',
    difficulty: 'Lanjutan',
    completed: false,
    inProgress: false,
    locked: true,
    progress: 0,
    topics: [
      'Prinsip cognitive restructuring',
      'Mengidentifikasi cognitive distortions',
      'Teknik challenging negative thoughts',
      'Membuat balanced thoughts'
    ],
    xpReward: 40
  }
])

// Computed properties
const totalModules = computed(() => modules.value.length)
const completedModules = computed(() => modules.value.filter(m => m.completed).length)
const inProgressModules = computed(() => modules.value.filter(m => m.inProgress).length)
const totalXPFromLearning = computed(() => 
  modules.value.filter(m => m.completed).reduce((total, m) => total + m.xpReward, 0)
)

const filteredModules = computed(() => {
  if (activeCategory.value === 'all') return modules.value
  return modules.value.filter(m => m.category === activeCategory.value)
})

const favoriteModules = computed(() => {
  return modules.value.filter(m => favoriteIds.value.includes(m.id))
})

const recentModules = computed(() => {
  const recentIds = recentAccess.value.map(r => r.id)
  return modules.value
    .filter(m => recentIds.includes(m.id))
    .map(m => {
      const accessInfo = recentAccess.value.find(r => r.id === m.id)
      return { ...m, lastAccessed: accessInfo.lastAccessed }
    })
    .sort((a, b) => {
      // Sort by most recent first
      const timeMap = { 
        '2 jam lalu': 2, 
        '1 hari lalu': 1, 
        '2 hari lalu': 0 
      }
      return (timeMap[b.lastAccessed] || 0) - (timeMap[a.lastAccessed] || 0)
    })
})

// Methods
const getModuleIcon = (category) => {
  const icons = {
    'self-acceptance': 'heart',
    'cbt': 'zap',
    'body-image': 'user',
    'mindfulness': 'sun'
  }
  return icons[category] || 'book'
}

const getModuleIconStyle = (category) => {
  const styles = {
    'self-acceptance': 'bg-pink-500',
    'cbt': 'bg-blue-500',
    'body-image': 'bg-purple-500',
    'mindfulness': 'bg-green-500'
  }
  return styles[category] || 'bg-gray-500'
}

const getButtonStyle = (module) => {
  if (module.locked) return 'bg-gray-100 text-gray-400 cursor-not-allowed'
  if (module.completed) return 'bg-green-100 text-green-700 hover:bg-green-200'
  if (module.inProgress) return 'bg-yellow-600 text-white hover:bg-yellow-700'
  return 'bg-purple-600 text-white hover:bg-purple-700'
}

const getButtonIcon = (module) => {
  if (module.locked) return 'lock'
  if (module.completed) return 'check-circle'
  if (module.inProgress) return 'play'
  return 'play-circle'
}

const getButtonText = (module) => {
  if (module.locked) return 'Terkunci'
  if (module.completed) return 'Ulangi'
  if (module.inProgress) return 'Lanjutkan'
  return 'Mulai Belajar'
}

const getCategoryProgress = (categoryId) => {
  const categoryModules = modules.value.filter(m => m.category === categoryId)
  if (categoryModules.length === 0) return 0
  
  const totalProgress = categoryModules.reduce((sum, m) => sum + m.progress, 0)
  return Math.round(totalProgress / categoryModules.length)
}

const openModule = (module) => {
  if (module.locked) {
    alert('Selesaikan modul sebelumnya untuk membuka modul ini.')
    return
  }
  
  // Add to recent access
  const existingIndex = recentAccess.value.findIndex(r => r.id === module.id)
  if (existingIndex !== -1) {
    recentAccess.value.splice(existingIndex, 1)
  }
  recentAccess.value.unshift({ id: module.id, lastAccessed: 'Baru saja' })
  
  // Keep only last 5 recent items
  if (recentAccess.value.length > 5) {
    recentAccess.value = recentAccess.value.slice(0, 5)
  }
  
  // Navigate to module detail page
  window.alert(`Opening module: ${module.title}`)
  // navigateTo(`/learning/${module.id}`)
  
  // For demo purpose, simulate completing module
  if (!module.completed && !module.inProgress) {
    module.inProgress = true
    module.progress = 10
  }
}

const toggleFavorite = (moduleId) => {
  const index = favoriteIds.value.indexOf(moduleId)
  if (index === -1) {
    favoriteIds.value.push(moduleId)
  } else {
    favoriteIds.value.splice(index, 1)
  }
}

const removeFavorite = (moduleId) => {
  const index = favoriteIds.value.indexOf(moduleId)
  if (index !== -1) {
    favoriteIds.value.splice(index, 1)
  }
}

const isFavorite = (moduleId) => {
  return favoriteIds.value.includes(moduleId)
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

/* Hide scrollbar for filter tabs */
.scrollbar-hide {
  -ms-overflow-style: none;  /* Internet Explorer 10+ */
  scrollbar-width: none;  /* Firefox */
}

.scrollbar-hide::-webkit-scrollbar {
  display: none;  /* Safari and Chrome */
}
</style>
