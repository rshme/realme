<template>
  <div class="min-h-screen bg-gray-50 flex flex-col">
    <!-- Header -->
    <div class="safe-area-top bg-white shadow-sm border-b border-gray-200">
      <div class="flex items-center justify-between p-4">
        <div class="flex items-center">
          <button @click="$router.back()" class="p-2 hover:bg-gray-100 rounded-full mr-2">
            <ArrowLeftIcon size="24" class="text-gray-600" />
          </button>
          <div class="flex items-center">
            <div class="w-10 h-10 bg-gradient-to-br from-purple-400 to-pink-500 rounded-full flex items-center justify-center mr-3">
              <UsersIcon size="20" class="text-white" />
            </div>
            <div>
              <h1 class="text-lg font-semibold text-gray-900">{{ circleName }}</h1>
              <p class="text-sm text-gray-500">{{ members.length }} anggota • {{ onlineCount }} online</p>
            </div>
          </div>
        </div>
        <div class="flex items-center space-x-2">
          <button @click="showMembers = true" class="p-2 hover:bg-gray-100 rounded-full">
            <UsersIcon size="20" class="text-gray-600" />
          </button>
          <button @click="showMenu = true" class="p-2 hover:bg-gray-100 rounded-full">
            <MoreVerticalIcon size="20" class="text-gray-600" />
          </button>
        </div>
      </div>
    </div>

    <!-- Messages Container -->
    <div ref="messagesContainer" class="flex-1 overflow-y-auto p-4 space-y-4 pb-6">
      <div 
        v-for="message in messages" 
        :key="message.id"
        class="flex"
        :class="message.isFromMe ? 'justify-end' : 'justify-start'"
      >
        <div class="max-w-[80%]">
          <!-- Message bubble -->
          <div 
            class="rounded-2xl px-4 py-3 break-words"
            :class="message.isFromMe 
              ? 'bg-purple-600 text-white rounded-br-md' 
              : 'bg-white border border-gray-200 text-gray-900 rounded-bl-md'"
          >
            <div v-if="!message.isFromMe" class="flex items-center mb-1">
              <div class="w-6 h-6 bg-gradient-to-br from-blue-400 to-purple-500 rounded-full flex items-center justify-center mr-2">
                <span class="text-white text-xs font-semibold">{{ message.author.avatar }}</span>
              </div>
              <span class="text-sm font-medium" :class="message.isFromMe ? 'text-purple-200' : 'text-gray-600'">
                {{ message.author.name }}
              </span>
            </div>
            <p class="text-sm leading-relaxed">{{ message.content }}</p>
            <div class="flex items-center justify-between mt-2">
              <span class="text-xs opacity-70">{{ message.timeAgo }}</span>
              <div v-if="message.isFromMe" class="flex items-center">
                <CheckIcon v-if="message.status === 'sent'" size="14" class="text-purple-200" />
                <div v-else-if="message.status === 'delivered'" class="flex">
                  <CheckIcon size="14" class="text-purple-200" />
                  <CheckIcon size="14" class="text-purple-200 -ml-1" />
                </div>
                <div v-else-if="message.status === 'read'" class="flex">
                  <CheckIcon size="14" class="text-blue-300" />
                  <CheckIcon size="14" class="text-blue-300 -ml-1" />
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Typing indicator -->
      <div v-if="isTyping" class="flex justify-start">
        <div class="max-w-[80%]">
          <div class="bg-white border border-gray-200 rounded-2xl rounded-bl-md px-4 py-3">
            <div class="flex items-center">
              <div class="w-6 h-6 bg-gradient-to-br from-green-400 to-blue-500 rounded-full flex items-center justify-center mr-2">
                <span class="text-white text-xs font-semibold">A</span>
              </div>
              <div class="flex space-x-1">
                <div class="w-2 h-2 bg-gray-400 rounded-full animate-bounce"></div>
                <div class="w-2 h-2 bg-gray-400 rounded-full animate-bounce" style="animation-delay: 0.1s;"></div>
                <div class="w-2 h-2 bg-gray-400 rounded-full animate-bounce" style="animation-delay: 0.2s;"></div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Message Input -->
    <div class="bg-white border-t border-gray-200 p-4 pb-2">
      <div class="flex items-center space-x-3">
        <button class="p-2 text-gray-500 hover:text-gray-700 rounded-full hover:bg-gray-100">
          <PlusIcon size="24" />
        </button>
        
        <div class="flex-1 relative">
          <textarea
            v-model="newMessage"
            @keydown.enter.exact.prevent="sendMessage"
            @keydown.enter.shift.exact="null"
            @input="handleTyping"
            placeholder="Ketik pesan..."
            class="w-full px-4 py-3 border border-gray-300 rounded-2xl focus:ring-2 focus:ring-purple-500 focus:border-transparent resize-none overflow-hidden"
            rows="1"
            ref="messageInput"
          ></textarea>
        </div>
        
        <button 
          @click="sendMessage"
          :disabled="!newMessage.trim()"
          class="p-3 bg-purple-600 text-white rounded-full hover:bg-purple-700 disabled:opacity-50 disabled:cursor-not-allowed transition-colors duration-200"
        >
          <SendIcon size="20" />
        </button>
      </div>
    </div>

    <!-- Members Modal -->
    <div v-if="showMembers" class="fixed inset-0 bg-black/50 flex items-end z-50">
      <div class="bg-white w-full max-h-[70vh] rounded-t-3xl p-6 animate-slide-up overflow-y-auto">
        <div class="flex items-center justify-between mb-6">
          <h3 class="text-xl font-semibold text-gray-900">Anggota Circle</h3>
          <button @click="showMembers = false" class="p-2 hover:bg-gray-100 rounded-full">
            <XIcon size="24" class="text-gray-600" />
          </button>
        </div>
        
        <div class="space-y-3">
          <div 
            v-for="member in members" 
            :key="member.id"
            class="flex items-center justify-between p-3 hover:bg-gray-50 rounded-xl"
          >
            <div class="flex items-center">
              <div class="w-12 h-12 bg-gradient-to-br from-blue-400 to-purple-500 rounded-full flex items-center justify-center mr-4">
                <span class="text-white font-semibold">{{ member.avatar }}</span>
              </div>
              <div>
                <div class="font-semibold text-gray-900">
                  {{ member.name }}
                  <span v-if="member.isYou" class="text-sm text-gray-500">(Anda)</span>
                </div>
                <div class="text-sm text-gray-600">
                  <span class="inline-flex items-center">
                    <div 
                      class="w-2 h-2 rounded-full mr-2"
                      :class="member.isOnline ? 'bg-green-500' : 'bg-gray-400'"
                    ></div>
                    {{ member.isOnline ? 'Online' : 'Terakhir dilihat ' + member.lastSeen }}
                  </span>
                </div>
              </div>
            </div>
            
            <div v-if="member.isAdmin" class="px-2 py-1 bg-purple-100 text-purple-700 text-xs rounded-full">
              Admin
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Menu Modal -->
    <div v-if="showMenu" class="fixed inset-0 bg-black/50 flex items-end z-50">
      <div class="bg-white w-full rounded-t-3xl p-6 animate-slide-up">
        <div class="flex items-center justify-between mb-6">
          <h3 class="text-xl font-semibold text-gray-900">Pengaturan Circle</h3>
          <button @click="showMenu = false" class="p-2 hover:bg-gray-100 rounded-full">
            <XIcon size="24" class="text-gray-600" />
          </button>
        </div>
        
        <div class="space-y-2">
          <button class="w-full flex items-center p-4 hover:bg-gray-50 rounded-xl text-left">
            <BellIcon size="20" class="text-gray-600 mr-4" />
            <span class="font-medium text-gray-900">Notifikasi</span>
          </button>
          
          <button class="w-full flex items-center p-4 hover:bg-gray-50 rounded-xl text-left">
            <FileIcon size="20" class="text-gray-600 mr-4" />
            <span class="font-medium text-gray-900">Media & File</span>
          </button>
          
          <button class="w-full flex items-center p-4 hover:bg-gray-50 rounded-xl text-left">
            <UserPlusIcon size="20" class="text-gray-600 mr-4" />
            <span class="font-medium text-gray-900">Undang Anggota</span>
          </button>
          
          <button class="w-full flex items-center p-4 hover:bg-red-50 rounded-xl text-left text-red-600">
            <LogOutIcon size="20" class="mr-4" />
            <span class="font-medium">Keluar dari Circle</span>
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, nextTick } from 'vue'

// Get route params
const route = useRoute()
const circleId = route.params.id
const circleName = ref(route.query.name || 'Safe Circle')

// Page meta
useHead({
  title: `${circleName.value} - RealMe`
})

// Reactive data
const showMembers = ref(false)
const showMenu = ref(false)
const newMessage = ref('')
const isTyping = ref(false)
const messagesContainer = ref(null)
const messageInput = ref(null)

// Sample members data
const members = ref([
  {
    id: 1,
    name: 'You',
    avatar: 'Y',
    isYou: true,
    isOnline: true,
    isAdmin: true,
    lastSeen: ''
  },
  {
    id: 2,
    name: 'Anna',
    avatar: 'AN',
    isYou: false,
    isOnline: true,
    isAdmin: false,
    lastSeen: ''
  },
  {
    id: 3,
    name: 'Mike',
    avatar: 'MK',
    isYou: false,
    isOnline: false,
    isAdmin: false,
    lastSeen: '5 menit lalu'
  },
  {
    id: 4,
    name: 'Lisa',
    avatar: 'LS',
    isYou: false,
    isOnline: true,
    isAdmin: false,
    lastSeen: ''
  }
])

// Sample messages data
const messages = ref([
  {
    id: 1,
    content: 'Halo semuanya! Senang bisa bergabung di circle ini 😊',
    author: { name: 'Anna', avatar: 'AN' },
    timeAgo: '2 jam lalu',
    isFromMe: false,
    status: 'read'
  },
  {
    id: 2,
    content: 'Halo Anna! Selamat datang. Semoga kita bisa saling support di sini ya',
    author: { name: 'You', avatar: 'Y' },
    timeAgo: '2 jam lalu',
    isFromMe: true,
    status: 'read'
  },
  {
    id: 3,
    content: 'Hari ini aku merasa lebih baik setelah mencoba teknik breathing yang kemarin kita diskusikan. Terima kasih teman-teman! 💜',
    author: { name: 'Lisa', avatar: 'LS' },
    timeAgo: '1 jam lalu',
    isFromMe: false,
    status: 'read'
  },
  {
    id: 4,
    content: 'Wah senang mendengarnya Lisa! Konsistensi memang kunci ya. Aku juga lagi coba rutin melakukan self-care routine',
    author: { name: 'You', avatar: 'Y' },
    timeAgo: '1 jam lalu',
    isFromMe: true,
    status: 'delivered'
  },
  {
    id: 5,
    content: 'Betul banget! Small steps tapi konsisten lebih baik daripada langkah besar tapi tidak berkelanjutan',
    author: { name: 'Mike', avatar: 'MK' },
    timeAgo: '45 menit lalu',
    isFromMe: false,
    status: 'read'
  }
])

// Computed
const onlineCount = computed(() => {
  return members.value.filter(member => member.isOnline).length
})

// Methods
const sendMessage = () => {
  if (!newMessage.value.trim()) return
  
  const message = {
    id: Date.now(),
    content: newMessage.value.trim(),
    author: { name: 'You', avatar: 'Y' },
    timeAgo: 'Baru saja',
    isFromMe: true,
    status: 'sent'
  }
  
  messages.value.push(message)
  newMessage.value = ''
  
  // Auto scroll to bottom
  nextTick(() => {
    scrollToBottom()
  })
  
  // Simulate message delivery
  setTimeout(() => {
    message.status = 'delivered'
  }, 1000)
  
  // Simulate someone typing (demo)
  setTimeout(() => {
    isTyping.value = true
    setTimeout(() => {
      isTyping.value = false
      // Add a response
      messages.value.push({
        id: Date.now() + 1,
        content: 'Setuju banget! Semangat terus ya 💪',
        author: { name: 'Anna', avatar: 'AN' },
        timeAgo: 'Baru saja',
        isFromMe: false,
        status: 'read'
      })
      nextTick(() => {
        scrollToBottom()
      })
    }, 2000)
  }, 3000)
}

const handleTyping = () => {
  // Auto-resize textarea
  if (messageInput.value) {
    messageInput.value.style.height = 'auto'
    messageInput.value.style.height = messageInput.value.scrollHeight + 'px'
  }
}

const scrollToBottom = () => {
  if (messagesContainer.value) {
    messagesContainer.value.scrollTop = messagesContainer.value.scrollHeight
  }
}

// Lifecycle
onMounted(() => {
  // Auto scroll to bottom on load
  nextTick(() => {
    scrollToBottom()
  })
})
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

.pb-safe {
  padding-bottom: env(safe-area-inset-bottom);
}

.safe-area-top {
  padding-top: env(safe-area-inset-top);
}

/* Custom scrollbar */
.overflow-y-auto::-webkit-scrollbar {
  width: 4px;
}

.overflow-y-auto::-webkit-scrollbar-track {
  background: transparent;
}

.overflow-y-auto::-webkit-scrollbar-thumb {
  background: rgba(156, 163, 175, 0.5);
  border-radius: 2px;
}

.overflow-y-auto::-webkit-scrollbar-thumb:hover {
  background: rgba(156, 163, 175, 0.7);
}
</style>
