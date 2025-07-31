<template>
  <div class="min-h-screen bg-gray-50">
    <!-- Header -->
    <div class="safe-area-top bg-white shadow-sm">
      <div class="flex items-center justify-between p-4">
        <button @click="$router.back()" class="p-2 hover:bg-gray-100 rounded-full">
          <FeatherIcon name="arrow-left" size="24" class="text-gray-600" />
        </button>
        <h1 class="text-xl font-semibold text-gray-900">Safe Community</h1>
        <button @click="showCreatePost = true" class="p-2 bg-purple-100 hover:bg-purple-200 rounded-full">
          <FeatherIcon name="plus" size="24" class="text-purple-600" />
        </button>
      </div>
    </div>
    
    <!-- Community Guidelines Banner -->
    <div class="px-6 py-4">
      <div class="bg-blue-50 border border-blue-200 rounded-xl p-4">
        <div class="flex items-start">
          <FeatherIcon name="shield" size="20" class="text-blue-600 mr-3 mt-0.5" />
          <div>
            <h3 class="font-semibold text-blue-900 mb-1">Komunitas yang Aman</h3>
            <p class="text-sm text-blue-700">
              Berbagi dengan hormat, saling support, dan jaga privasi bersama.
            </p>
          </div>
        </div>
      </div>
    </div>
    
    <!-- Filter Tabs -->
    <div class="px-6 mb-4">
      <div class="flex space-x-2">
        <button 
          v-for="filter in filters" 
          :key="filter.id"
          @click="activeFilter = filter.id"
          class="px-4 py-2 rounded-full text-sm font-medium transition-colors duration-200"
          :class="activeFilter === filter.id ? 'bg-purple-600 text-white' : 'bg-white text-gray-700 hover:bg-purple-50'"
        >
          {{ filter.label }}
        </button>
      </div>
    </div>
    
    <!-- Posts Feed -->
    <div class="px-6 space-y-4 mb-20">
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
              <FeatherIcon :name="post.isLiked ? 'heart' : 'heart'" size="18" :class="post.isLiked ? 'fill-current' : ''" />
              <span class="text-sm">{{ post.likes }}</span>
            </button>
            
            <button 
              @click="showComments(post)"
              class="flex items-center space-x-2 text-gray-600 hover:text-blue-500 transition-colors duration-200"
            >
              <FeatherIcon name="message-circle" size="18" />
              <span class="text-sm">{{ post.comments }}</span>
            </button>
            
            <button 
              @click="sharePost(post)"
              class="flex items-center space-x-2 text-gray-600 hover:text-green-500 transition-colors duration-200"
            >
              <FeatherIcon name="share-2" size="18" />
              <span class="text-sm">Bagikan</span>
            </button>
          </div>
          
          <button class="text-gray-400 hover:text-gray-600">
            <FeatherIcon name="more-horizontal" size="18" />
          </button>
        </div>
      </div>
    </div>
    
    <!-- Create Post Modal -->
    <div v-if="showCreatePost" class="fixed inset-0 bg-black/50 flex items-end z-50">
      <div class="bg-white w-full max-h-[90vh] rounded-t-3xl p-6 animate-slide-up">
        <div class="flex items-center justify-between mb-6">
          <h3 class="text-xl font-semibold text-gray-900">Buat Postingan</h3>
          <button @click="showCreatePost = false" class="p-2 hover:bg-gray-100 rounded-full">
            <FeatherIcon name="x" size="24" class="text-gray-600" />
          </button>
        </div>
        
        <form @submit.prevent="createPost" class="space-y-4">
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-2">Kategori</label>
            <select v-model="newPost.category" required class="input-field">
              <option value="">Pilih kategori</option>
              <option value="Self-Acceptance">Self-Acceptance</option>
              <option value="Body Image">Body Image</option>
              <option value="Mental Health">Mental Health</option>
              <option value="Motivasi">Motivasi</option>
              <option value="Curhat">Curhat</option>
            </select>
          </div>
          
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-2">Judul (Opsional)</label>
            <input 
              v-model="newPost.title"
              type="text" 
              class="input-field"
              placeholder="Judul postingan..."
            />
          </div>
          
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-2">Ceritakan pengalamanmu</label>
            <textarea 
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
              <FeatherIcon name="send" size="16" class="mr-2" />
              Posting
            </button>
          </div>
        </form>
      </div>
    </div>
    
    <!-- Bottom Navigation -->
    <div class="fixed bottom-0 left-0 right-0 bg-white border-t border-gray-200 safe-area-bottom">
      <div class="max-w-md mx-auto px-6 py-3">
        <div class="flex justify-around">
          <NuxtLink to="/dashboard" class="flex flex-col items-center p-2 text-gray-400 hover:text-gray-600">
            <FeatherIcon name="home" size="24" />
            <span class="text-xs mt-1">Home</span>
          </NuxtLink>
          <NuxtLink to="/journal" class="flex flex-col items-center p-2 text-gray-400 hover:text-gray-600">
            <FeatherIcon name="book-open" size="24" />
            <span class="text-xs mt-1">Journal</span>
          </NuxtLink>
          <NuxtLink to="/community" class="flex flex-col items-center p-2 text-purple-600">
            <FeatherIcon name="users" size="24" />
            <span class="text-xs mt-1 font-medium">Komunitas</span>
          </NuxtLink>
          <NuxtLink to="/learning" class="flex flex-col items-center p-2 text-gray-400 hover:text-gray-600">
            <FeatherIcon name="book" size="24" />
            <span class="text-xs mt-1">Belajar</span>
          </NuxtLink>
          <NuxtLink to="/profile" class="flex flex-col items-center p-2 text-gray-400 hover:text-gray-600">
            <FeatherIcon name="user" size="24" />
            <span class="text-xs mt-1">Profil</span>
          </NuxtLink>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, computed } from 'vue'

// Meta untuk halaman community
useHead({
  title: 'Safe Community - RealMe',
  meta: [
    { name: 'description', content: 'Bergabung dengan komunitas yang aman dan supportif. Berbagi cerita dan dapatkan dukungan dari teman sebaya.' }
  ]
})

// Filters
const filters = [
  { id: 'all', label: 'Semua' },
  { id: 'self-acceptance', label: 'Self-Acceptance' },
  { id: 'body-image', label: 'Body Image' },
  { id: 'mental-health', label: 'Mental Health' },
  { id: 'motivasi', label: 'Motivasi' }
]

const activeFilter = ref('all')
const showCreatePost = ref(false)

// Sample posts data
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

// New post form
const newPost = reactive({
  category: '',
  title: '',
  content: '',
  isAnonymous: false
})

// Computed
const filteredPosts = computed(() => {
  if (activeFilter.value === 'all') return posts.value
  return posts.value.filter(post => 
    post.category.toLowerCase().replace(' ', '-') === activeFilter.value
  )
})

// Methods
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

const toggleLike = (postId) => {
  const post = posts.value.find(p => p.id === postId)
  if (post) {
    post.isLiked = !post.isLiked
    post.likes += post.isLiked ? 1 : -1
  }
}

const showComments = (post) => {
  // Navigate to comments page
  console.log('Show comments for post:', post.id)
}

const sharePost = (post) => {
  // Share functionality
  console.log('Share post:', post.id)
  alert('Link postingan disalin ke clipboard!')
}

const createPost = () => {
  // Create new post
  const post = {
    id: posts.value.length + 1,
    author: { 
      name: newPost.isAnonymous ? 'Anonim' : 'Rafi S.',
      avatar: newPost.isAnonymous ? '?' : 'R'
    },
    timeAgo: 'Baru saja',
    category: newPost.category,
    title: newPost.title,
    content: newPost.content,
    isAnonymous: newPost.isAnonymous,
    likes: 0,
    comments: 0,
    isLiked: false
  }
  
  posts.value.unshift(post)
  
  // Reset form
  newPost.category = ''
  newPost.title = ''
  newPost.content = ''
  newPost.isAnonymous = false
  
  showCreatePost.value = false
  
  alert('Postingan berhasil dibuat! +5 XP')
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
</style>
