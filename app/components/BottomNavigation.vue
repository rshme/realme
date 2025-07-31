<template>
  <div class="fixed bottom-0 left-0 right-0 bg-white border-t border-gray-200 safe-area-bottom">
    <div class="max-w-md mx-auto px-6 py-3">
      <div class="flex justify-around">
        <NuxtLink 
          to="/dashboard" 
          class="flex flex-col items-center p-2 transition-colors duration-200"
          :class="currentRoute === 'dashboard' ? 'text-purple-600' : 'text-gray-400 hover:text-gray-600'"
        >
          <HomeIcon size="24" />
          <span class="text-xs mt-1" :class="currentRoute === 'dashboard' ? 'font-medium' : ''">Home</span>
        </NuxtLink>
        
        <NuxtLink 
          to="/journal" 
          class="flex flex-col items-center p-2 transition-colors duration-200"
          :class="currentRoute === 'journal' ? 'text-purple-600' : 'text-gray-400 hover:text-gray-600'"
        >
          <BookOpenIcon size="24" />
          <span class="text-xs mt-1" :class="currentRoute === 'journal' ? 'font-medium' : ''">Journal</span>
        </NuxtLink>
        
        <NuxtLink 
          to="/community" 
          class="flex flex-col items-center p-2 transition-colors duration-200"
          :class="currentRoute === 'community' ? 'text-purple-600' : 'text-gray-400 hover:text-gray-600'"
        >
          <UsersIcon size="24" />
          <span class="text-xs mt-1" :class="currentRoute === 'community' ? 'font-medium' : ''">Komunitas</span>
        </NuxtLink>
        
        <NuxtLink 
          to="/learning" 
          class="flex flex-col items-center p-2 transition-colors duration-200"
          :class="currentRoute === 'learning' ? 'text-purple-600' : 'text-gray-400 hover:text-gray-600'"
        >
          <BookIcon size="24" />
          <span class="text-xs mt-1" :class="currentRoute === 'learning' ? 'font-medium' : ''">Belajar</span>
        </NuxtLink>
        
        <NuxtLink 
          to="/profile" 
          class="flex flex-col items-center p-2 transition-colors duration-200"
          :class="currentRoute === 'profile' ? 'text-purple-600' : 'text-gray-400 hover:text-gray-600'"
        >
          <UserIcon size="24" />
          <span class="text-xs mt-1" :class="currentRoute === 'profile' ? 'font-medium' : ''">Profil</span>
        </NuxtLink>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'

// Get current route to highlight active navigation item
const route = useRoute()

const currentRoute = computed(() => {
  const path = route.path
  if (path === '/dashboard') return 'dashboard'
  if (path === '/journal') return 'journal'
  if (path === '/community') return 'community'
  if (path === '/learning') return 'learning'
  if (path === '/profile') return 'profile'
  return ''
})
</script>
