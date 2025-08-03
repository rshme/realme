<template>
  <div class="min-h-screen bg-gray-50">
    <!-- Header -->
    <div class="safe-area-top bg-white shadow-sm">
      <div class="flex items-center justify-start p-4">
        <div class="w-4/12">
          <button @click="$router.back()" class="p-2 hover:bg-gray-100 rounded-full">
          <ArrowLeftIcon size="24" class="text-gray-600" />
          </button>
        </div>
        <div class="w-8/12">
          <h1 class="text-xl font-semibold text-gray-900">Community</h1>
        </div>
      </div>
    </div>

    <!-- Main Tabs -->
    <div class="px-6 py-2">
      <div class="flex space-x-1 bg-gray-100 rounded-xl p-1">
        <button 
          @click="activeTab = 'all-posts'"
          class="flex-1 py-3 px-4 rounded-lg font-medium text-sm transition-all duration-200"
          :class="activeTab === 'all-posts' ? 'bg-white text-purple-600 shadow-sm' : 'text-gray-600 hover:text-gray-900'"
        >
          <UsersIcon size="18" class="inline mr-2" />
          All Posts
        </button>
        <button 
          @click="activeTab = 'safe-circle'"
          class="flex-1 py-3 px-4 rounded-lg font-medium text-sm transition-all duration-200"
          :class="activeTab === 'safe-circle' ? 'bg-white text-purple-600 shadow-sm' : 'text-gray-600 hover:text-gray-900'"
        >
          <ShieldIcon size="18" class="inline mr-2" />
          Safe Circle
        </button>
      </div>
    </div>
    
    <!-- All Posts Tab Content -->
    <div v-if="activeTab === 'all-posts'">
      <!-- Community Guidelines Banner -->
      <div class="px-6 py-4">
        <div class="bg-blue-50 border border-blue-200 rounded-xl p-4">
          <div class="flex items-start">
            <ShieldIcon size="20" class="text-blue-600 mr-3 mt-0.5" />
            <div>
              <h3 class="font-semibold text-blue-900 mb-1">Komunitas yang Aman</h3>
              <p class="text-sm text-blue-700">
                Berbagi dengan hormat, saling support, dan jaga privasi bersama.
              </p>
            </div>
          </div>
        </div>
      </div>

      <!-- CTA Button to Open Journal Modal -->
      <div class="flex justify-center px-6 py-2 mb-6">
        <button 
            @click="showCreatePost = true"
            class="bg-purple-600 text-white font-semibold px-8 py-3 rounded-xl shadow-lg hover:shadow-xl transition-all duration-300 transform hover:scale-105"
          >
            <PenToolIcon size="18" class="inline mr-2" />
            Buat postinganmu hari ini
          </button>
      </div>
      
      <!-- Filter Tabs -->
      <div class="px-6 mb-4">
        <div class="flex space-x-2 overflow-x-auto scrollbar-hide pb-2">
          <button 
            v-for="filter in filters" 
            :key="filter.id"
            @click="activeFilter = filter.id"
            class="flex-shrink-0 px-4 py-2 rounded-full text-sm font-medium transition-colors duration-200"
            :class="activeFilter === filter.id ? 'bg-purple-600 text-white' : 'bg-white text-gray-700 hover:bg-purple-50'"
          >
            {{ filter.label }}
          </button>
        </div>
      </div>
      
      <!-- Posts Feed -->
      <div class="px-6 space-y-4 mb-20 pb-8">
        <div 
          v-for="post in filteredPosts" 
          :key="post.id"
          class="bg-white rounded-2xl p-6 shadow-sm border border-gray-100"
        >
          <!-- Post Header -->
          <div class="flex items-center justify-between mb-4">
            <div class="flex items-center">
              <div class="w-10 h-10 bg-gradient-to-br from-purple-400 to-pink-500 rounded-full flex items-center justify-center mr-3">
                <span class="text-white font-semibold">{{ post.author.avatar }}</span>
              </div>
              <div>
                <div class="font-semibold text-gray-900">{{ post.author.name }}</div>
                <div class="text-sm text-gray-500">{{ post.timeAgo }}</div>
              </div>
            </div>
            <div class="flex items-center space-x-2">
              <span 
                v-if="post.isAnonymous" 
                class="px-2 py-1 bg-gray-100 text-gray-600 text-xs rounded-full"
              >
                Anonim
              </span>
              <span 
                class="px-2 py-1 text-xs rounded-full"
                :class="getCategoryStyle(post.category)"
              >
                {{ post.category }}
              </span>
            </div>
          </div>
          
          <!-- Post Content -->
          <div class="mb-4">
            <h3 v-if="post.title" class="font-semibold text-gray-900 mb-2">{{ post.title }}</h3>
            <p class="text-gray-700 leading-relaxed">{{ post.content }}</p>
          </div>
          
          <!-- Post Actions -->
          <div class="flex items-center justify-between pt-4 border-t border-gray-100">
            <div class="flex items-center space-x-4">
              <button 
                @click="toggleLike(post.id)"
                class="flex items-center space-x-2 text-gray-600 hover:text-red-500 transition-colors duration-200"
                :class="post.isLiked ? 'text-red-500' : ''"
              >
                <HeartIcon size="18" :class="post.isLiked ? 'fill-current' : ''" />
                <span class="text-sm">{{ post.likes }}</span>
              </button>
              
              <button 
                @click="showComments(post)"
                class="flex items-center space-x-2 text-gray-600 hover:text-blue-500 transition-colors duration-200"
              >
                <MessageCircleIcon size="18" />
                <span class="text-sm">{{ post.comments }}</span>
              </button>
              
              <button 
                @click="sharePost(post)"
                class="flex items-center space-x-2 text-gray-600 hover:text-green-500 transition-colors duration-200"
              >
                <Share2Icon size="18" />
                <span class="text-sm">Bagikan</span>
              </button>
            </div>
            
            <button class="text-gray-400 hover:text-gray-600">
              <MoreHorizontalIcon size="18" />
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- Safe Circle Tab Content -->
    <div v-if="activeTab === 'safe-circle'">
      <!-- Safe Circle Header -->
      <div class="px-6 py-4">
        <div class="bg-gradient-to-r from-green-50 to-blue-50 border border-green-200 rounded-xl p-4">
          <div class="flex items-start justify-between">
            <div class="flex items-start">
              <div class="w-10 h-10 bg-green-100 rounded-full flex items-center justify-center mr-3">
                <ShieldIcon size="20" class="text-green-600" />
              </div>
              <div>
                <h3 class="font-semibold text-green-900 mb-1">Safe Circle</h3>
                <p class="text-sm text-green-700">
                  Grup kecil 3-5 orang untuk berbagi cerita personal dengan lebih intim dan aman.
                </p>

                <button 
                  @click="showCreateCircle = true"
                  class="px-4 py-2 mt-3 bg-green-600 text-white text-sm font-medium rounded-lg hover:bg-green-700 transition-colors duration-200"
                >
                  <PlusIcon size="16" class="inline mr-1" />
                  Buat Circle
              </button>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- My Circles -->
      <div class="px-6 mb-6">
        <h4 class="text-lg font-semibold text-gray-900 mb-4">Circle Saya</h4>
        <div class="space-y-3">
          <div 
            v-for="circle in myCircles" 
            :key="circle.id"
            class="bg-white rounded-xl p-4 shadow-sm border border-gray-100 hover:shadow-md transition-shadow duration-200 cursor-pointer"
            @click="openCircle(circle)"
          >
            <div class="flex items-center justify-between">
              <div class="flex items-center">
                <div class="w-12 h-12 bg-gradient-to-br from-purple-400 to-pink-500 rounded-full flex items-center justify-center mr-4">
                  <UsersIcon size="20" class="text-white" />
                </div>
                <div>
                  <h5 class="font-semibold text-gray-900">{{ circle.name }}</h5>
                  <p class="text-sm text-gray-600">{{ circle.members.length }}/{{ circle.maxMembers }} anggota</p>
                  <p class="text-xs text-gray-500">{{ circle.lastActivity }}</p>
                </div>
              </div>
              <div class="flex items-center">
                <div class="flex -space-x-2 mr-3">
                  <div 
                    v-for="member in circle.members.slice(0, 3)" 
                    :key="member.name"
                    class="w-8 h-8 bg-gradient-to-br from-blue-400 to-purple-500 rounded-full flex items-center justify-center border-2 border-white"
                  >
                    <span class="text-white text-xs font-semibold">{{ member.avatar }}</span>
                  </div>
                  <div 
                    v-if="circle.members.length > 3"
                    class="w-8 h-8 bg-gray-300 rounded-full flex items-center justify-center border-2 border-white"
                  >
                    <span class="text-gray-600 text-xs">+{{ circle.members.length - 3 }}</span>
                  </div>
                </div>
                <ChevronRightIcon size="16" class="text-gray-400" />
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Available Circles to Join -->
      <div class="px-6 mb-20">
        <h4 class="text-lg font-semibold text-gray-900 mb-4">Circle Terbuka</h4>
        <div class="space-y-3">
          <div 
            v-for="circle in availableCircles" 
            :key="circle.id"
            class="bg-white rounded-xl p-4 shadow-sm border border-gray-100"
          >
            <div class="flex items-center justify-between">
              <div class="flex items-start">
                <div class="w-12 h-12 bg-gradient-to-br from-green-400 to-blue-500 rounded-full flex items-center justify-center mr-4">
                  <UsersIcon size="20" class="text-white" />
                </div>
                <div>
                  <h5 class="font-semibold text-gray-900">{{ circle.name }}</h5>
                  <p class="text-sm text-gray-600">{{ circle.description }}</p>
                  <p class="text-xs text-gray-500">{{ circle.members.length }}/{{ circle.maxMembers }} anggota</p>

                  <button 
                    @click="joinCircle(circle.id)"
                    class="px-4 py-2 mt-3 bg-purple-600 hover:bg-purple-700 text-white text-sm font-medium rounded-lg transition-colors duration-200"
                  >
                    Gabung
                  </button>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    
    <!-- Create Circle Modal -->
    <div v-if="showCreateCircle" class="fixed inset-0 bg-black/50 flex items-end z-50">
      <div class="bg-white w-full max-h-[90vh] rounded-t-3xl p-6 animate-slide-up overflow-y-auto">
        <div class="flex items-center justify-between mb-6">
          <h3 class="text-xl font-semibold text-gray-900">Buat Safe Circle</h3>
          <button @click="showCreateCircle = false" class="p-2 hover:bg-gray-100 rounded-full">
            <XIcon size="24" class="text-gray-600" />
          </button>
        </div>
        
        <form @submit.prevent="createCircle" class="space-y-4">
          <div>
            <label for="circle-name" class="block text-sm font-medium text-gray-700 mb-2">Nama Circle</label>
            <input 
              id="circle-name"
              v-model="newCircle.name"
              type="text" 
              required
              class="input-field"
              placeholder="Misal: Self-Love Journey, Body Positive Squad..."
              maxlength="50"
            />
          </div>
          
          <div>
            <label for="circle-description" class="block text-sm font-medium text-gray-700 mb-2">Deskripsi</label>
            <textarea 
              id="circle-description"
              v-model="newCircle.description"
              required
              class="input-field resize-none"
              rows="3"
              placeholder="Jelaskan tujuan dan fokus circle ini..."
              maxlength="200"
            ></textarea>
          </div>
          
          <div>
            <label for="max-members" class="block text-sm font-medium text-gray-700 mb-2">Maksimal Anggota</label>
            <select id="max-members" v-model="newCircle.maxMembers" required class="input-field">
              <option value="3">3 orang</option>
              <option value="4">4 orang</option>
              <option value="5">5 orang</option>
            </select>
          </div>
          
          <div class="flex items-center justify-between">
            <div class="flex items-center">
              <input 
                id="private-circle"
                v-model="newCircle.isPrivate"
                type="checkbox" 
                class="h-4 w-4 text-purple-600 focus:ring-purple-500 border-gray-300 rounded"
              />
              <label for="private-circle" class="ml-2 text-sm text-gray-700">
                Circle privat (hanya bisa bergabung dengan undangan)
              </label>
            </div>
          </div>
          
          <div class="flex space-x-3 pt-4">
            <button 
              type="button"
              @click="showCreateCircle = false"
              class="btn-secondary flex-1"
            >
              Batal
            </button>
            <button 
              type="submit"
              :disabled="!newCircle.name.trim() || !newCircle.description.trim()"
              class="btn-primary flex-1"
              :class="!newCircle.name.trim() || !newCircle.description.trim() ? 'opacity-50 cursor-not-allowed' : ''"
            >
              Buat Circle
            </button>
          </div>
        </form>
      </div>
    </div>
    
    <!-- Create Post Modal -->
    <div v-if="showCreatePost" class="fixed inset-0 bg-black/50 flex items-end z-50">
      <div class="bg-white w-full max-h-[90vh] rounded-t-3xl p-6 animate-slide-up">
        <div class="flex items-center justify-between mb-6">
          <h3 class="text-xl font-semibold text-gray-900">Buat Postingan</h3>
          <button @click="showCreatePost = false" class="p-2 hover:bg-gray-100 rounded-full">
            <XIcon size="24" class="text-gray-600" />
          </button>
        </div>
        
        <form @submit.prevent="createPost" class="space-y-4">
          <div>
            <label for="post-category" class="block text-sm font-medium text-gray-700 mb-2">Kategori</label>
            <select id="post-category" v-model="newPost.category" required class="input-field">
              <option value="">Pilih kategori</option>
              <option value="Self-Acceptance">Self-Acceptance</option>
              <option value="Body Image">Body Image</option>
              <option value="Mental Health">Mental Health</option>
              <option value="Motivasi">Motivasi</option>
              <option value="Curhat">Curhat</option>
            </select>
          </div>
          
          <div>
            <label for="post-title" class="block text-sm font-medium text-gray-700 mb-2">Judul (Opsional)</label>
            <input 
              id="post-title"
              v-model="newPost.title"
              type="text" 
              class="input-field"
              placeholder="Judul postingan..."
            />
          </div>
          
          <div>
            <label for="post-content" class="block text-sm font-medium text-gray-700 mb-2">Ceritakan pengalamanmu</label>
            <textarea 
              id="post-content"
              v-model="newPost.content"
              required
              class="input-field resize-none"
              rows="6"
              placeholder="Berbagi pengalaman, perasaan, atau dukungan untuk komunitas..."
            ></textarea>
          </div>
          
          <div class="flex items-center justify-between">
            <div class="flex items-center">
              <input 
                id="anonymous"
                v-model="newPost.isAnonymous"
                type="checkbox" 
                class="h-4 w-4 text-purple-600 focus:ring-purple-500 border-gray-300 rounded"
              />
              <label for="anonymous" class="ml-2 text-sm text-gray-700">
                Posting secara anonim
              </label>
            </div>
          </div>
          
          <div class="flex space-x-3">
            <button 
              type="button"
              @click="showCreatePost = false"
              class="btn-secondary flex-1"
            >
              Batal
            </button>
            <button 
              type="submit"
              :disabled="!newPost.content.trim() || !newPost.category"
              class="btn-primary flex-1"
              :class="!newPost.content.trim() || !newPost.category ? 'opacity-50 cursor-not-allowed' : ''"
            >
              <div class="w-full flex justify-center items-center">
                <SendIcon size="16" class="mr-2" />
                <span class="inline">Posting</span>
              </div>
            </button>
          </div>
        </form>
      </div>
    </div>
    
    <!-- Bottom Navigation -->
    <BottomNavigation />
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

// Page meta
useHead({
  title: 'Community - RealMe'
})

// Reactive data
const activeTab = ref('all-posts')
const showCreateModal = ref(false)
const showCreateCircle = ref(false)
const activeFilter = ref('all')
const showCreatePost = ref(false)
const newPost = ref({
  title: '',
  content: '',
  category: 'sharing',
  isAnonymous: false
})
const newCircle = ref({
  name: '',
  description: '',
  maxMembers: 5
})

// Filters
const filters = [
  { id: 'all', label: 'Semua' },
  { id: 'self-acceptance', label: 'Self-Acceptance' },
  { id: 'body-image', label: 'Body Image' },
  { id: 'mental-health', label: 'Mental Health' },
  { id: 'motivasi', label: 'Motivasi' }
]

// Sample posts
const posts = ref([
  {
    id: 1,
    author: { name: 'Sarah M.', avatar: 'S' },
    timeAgo: '2 jam lalu',
    category: 'Self-Acceptance',
    title: 'Belajar mencintai tubuhku apa adanya',
    content: 'Setelah bertahun-tahun insecure dengan bentuk tubuhku, akhirnya aku mulai belajar untuk menerima dan mencintai diriku apa adanya. Perjalanan ini tidak mudah, tapi dengan dukungan kalian semua di sini, aku merasa lebih kuat. Terima kasih sudah menjadi safe space untukku! 💜',
    isAnonymous: false,
    likes: 24,
    comments: 8,
    isLiked: false
  },
  {
    id: 2,
    author: { name: 'Anonim', avatar: '?' },
    timeAgo: '4 jam lalu',
    category: 'Curhat',
    content: 'Hari ini aku sempat down karena komentar seseorang tentang penampilanku. Tapi aku ingat apa yang aku pelajari di RealMe, bahwa pendapat orang lain tidak menentukan nilai diriku. Aku adalah aku, dan itu sudah cukup. Semangat untuk kita semua yang sedang berjuang! ✨',
    isAnonymous: true,
    likes: 31,
    comments: 12,
    isLiked: true
  },
  {
    id: 3,
    author: { name: 'Rani K.', avatar: 'R' },
    timeAgo: '6 jam lalu',
    category: 'Motivasi',
    title: 'Small wins matter!',
    content: 'Kemarin aku berhasil makan teratur 3 kali sehari dan tidak skip meals. Mungkin terdengar sepele, tapi buat aku ini pencapaian besar! Kadang kita lupa bahwa progress itu tidak selalu harus besar. Small steps juga penting. Apa small win kalian hari ini?',
    isAnonymous: false,
    likes: 18,
    comments: 15,
    isLiked: false
  },
  {
    id: 4,
    author: { name: 'Anonim', avatar: '?' },
    timeAgo: '1 hari lalu',
    category: 'Body Image',
    content: 'Sharing tips untuk teman-teman yang struggle dengan body image: 1) Unfollowing akun yang bikin insecure, 2) Follow body positive accounts, 3) Practice gratitude untuk tubuh kita, 4) Remember bahwa social media is not reality. Semoga membantu! 🌸',
    isAnonymous: true,
    likes: 42,
    comments: 6,
    isLiked: true
  }
])

// Safe Circle data
const myCircles = ref([
  {
    id: 1,
    name: 'College Support',
    members: [
      { name: 'You', avatar: 'Y', isYou: true },
      { name: 'Anna', avatar: 'AN' },
      { name: 'Mike', avatar: 'MK' },
      { name: 'Lisa', avatar: 'LS' }
    ],
    lastActivity: '2 jam lalu'
  },
  {
    id: 2,
    name: 'Anxiety Warriors',
    members: [
      { name: 'You', avatar: 'Y', isYou: true },
      { name: 'Sarah', avatar: 'SR' },
      { name: 'David', avatar: 'DV' }
    ],
    lastActivity: '1 hari lalu'
  }
])

const availableCircles = ref([
  {
    id: 3,
    name: 'New Parent Support',
    description: 'Safe space untuk parents baru berbagi pengalaman',
    members: 3,
    maxMembers: 5
  },
  {
    id: 4,
    name: 'Career Transition',
    description: 'Dukungan untuk yang sedang career change',
    members: 2,
    maxMembers: 4
  }
])

// Computed
const filteredPosts = computed(() => {
  if (activeFilter.value === 'all') return posts.value
  return posts.value.filter(post => 
    post.category.toLowerCase().replace(' ', '-') === activeFilter.value
  )
})

// Methods
const switchTab = (tabId) => {
  activeTab.value = tabId
}

const openCreateModal = () => {
  showCreateModal.value = true
}

const closeCreateModal = () => {
  showCreateModal.value = false
  resetForm()
}

const openCreateCircleModal = () => {
  showCreateCircle.value = true
}

const closeCreateCircleModal = () => {
  showCreateCircle.value = false
  resetCircleForm()
}

const resetForm = () => {
  newPost.value = {
    title: '',
    content: '',
    category: 'sharing',
    isAnonymous: false
  }
}

const resetCircleForm = () => {
  newCircle.value = {
    name: '',
    description: '',
    maxMembers: 5
  }
}

const createPost = () => {
  if (!newPost.value.content.trim()) return
  
  const post = {
    id: Date.now(),
    title: newPost.value.title,
    content: newPost.value.content,
    author: {
      name: newPost.value.isAnonymous ? 'Anonim' : 'Jane Poe',
      avatar: newPost.value.isAnonymous ? '?' : 'JP'
    },
    category: newPost.value.category,
    timeAgo: 'Baru saja',
    likes: 0,
    comments: 0,
    isLiked: false,
    isAnonymous: newPost.value.isAnonymous
  }
  
  posts.value.unshift(post)
  closeCreateModal()
  showCreatePost.value = false
}

const createCircle = () => {
  if (!newCircle.value.name.trim() || !newCircle.value.description.trim()) return
  
  const circle = {
    id: Date.now(),
    name: newCircle.value.name,
    members: [
      { name: 'You', avatar: 'Y', isYou: true }
    ],
    lastActivity: 'Baru dibuat'
  }
  
  myCircles.value.unshift(circle)
  closeCreateCircleModal()
}

const joinCircle = (circleId) => {
  const circle = availableCircles.value.find(c => c.id === circleId)
  if (circle && circle.members < circle.maxMembers) {
    // Move to my circles
    const newCircle = {
      id: circle.id,
      name: circle.name,
      members: [
        { name: 'You', avatar: 'Y', isYou: true }
      ],
      lastActivity: 'Baru bergabung'
    }
    
    myCircles.value.push(newCircle)
    // Remove from available circles
    availableCircles.value = availableCircles.value.filter(c => c.id !== circleId)
  }
}

const toggleLike = (postId) => {
  const post = posts.value.find(p => p.id === postId)
  if (post) {
    post.isLiked = !post.isLiked
    post.likes += post.isLiked ? 1 : -1
  }
}

const showComments = (post) => {
  console.log('Show comments for post:', post.id)
}

const sharePost = (post) => {
  console.log('Share post:', post.id)
  alert('Link postingan disalin ke clipboard!')
}

const openCircle = (circle) => {
  // Navigate to group chat page with circle information
  navigateTo(`/circle-chat/${circle.id}?name=${encodeURIComponent(circle.name)}`)
}

const getCategoryStyle = (category) => {
  const styles = {
    'Self-Acceptance': 'bg-purple-100 text-purple-700',
    'Body Image': 'bg-pink-100 text-pink-700',
    'Mental Health': 'bg-blue-100 text-blue-700',
    'Motivasi': 'bg-green-100 text-green-700',
    'Curhat': 'bg-orange-100 text-orange-700'
  }
  return styles[category] || 'bg-gray-100 text-gray-700'
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

.fill-current {
  fill: currentColor;
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
