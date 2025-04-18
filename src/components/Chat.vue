<script setup>
import { ref, onMounted } from 'vue'
import Header from './Header.vue'
import Typing from './Typing.vue'
import Footer from './Footer.vue'

// Chat state
const messages = ref([])
const newMessage = ref('')
const showOptions = ref(false)
const optionsType = ref('')
const options = ref({
  role: ['Help', 'Support']
})
const isTyping = ref(false)

// Add a bot message with typing indicator
const addBotMessage = (text, delay = 1000) => {
  isTyping.value = true

  setTimeout(() => {
    messages.value.push({
      sender: 'bot',
      text,
      timestamp: new Date()
    })
    isTyping.value = false
  }, delay)
}

// Add a user message
const addUserMessage = (text) => {
  messages.value.push({
    sender: 'user',
    text,
    timestamp: new Date()
  })
  newMessage.value = ''
}

// Handle sending a message
const sendMessage = () => {
  if (!newMessage.value.trim()) return
  addUserMessage(newMessage.value)
  handleResponse()
}

// Show options for the user to select
const showUserOptions = (type) => {
  optionsType.value = type
  showOptions.value = true
}

// Handle user option selection
const selectOption = (option) => {
  addUserMessage(option)
  showOptions.value = false

  setTimeout(() => {
    addBotMessage('Our tool is great for support teams of all sizes! How many people are on yours?')
  }, 1000)
}

// Handle bot responses based on conversation
const handleResponse = () => {
  const lastUserMessage = messages.value.filter((m) => m.sender === 'user').pop()

  if (lastUserMessage && lastUserMessage.text.includes('test')) {
    addBotMessage(
      'That is a lot of people! DuckChat 3000 is perfect for teams of all sizes. We can help you scale your support operations efficiently.'
    )
  }
}

// Initialize chat
onMounted(() => {
  addBotMessage('Quack quack!!', 500)

  setTimeout(() => {
    addBotMessage("What's quackin my baby ducklett?", 1000)
    showUserOptions('role')
  }, 1500)
})
</script>

<template>
  <div class="max-w-md mx-auto bg-white rounded-lg shadow-lg overflow-hidden">
    <Header />

    <!-- Chat Messages -->
    <div class="h-96 overflow-y-auto p-4">
      <div v-for="(message, index) in messages" :key="index" class="mb-1">
        <div v-if="message.sender === 'bot'" class="mb-1">
          <div
            v-if="index === 0 || messages[index - 1].sender !== 'bot'"
            class="text-sm text-gray-500 mb-1 ml-8"
          >
            DuckChat 3000
          </div>
          <div class="flex items-start">
            <div class="w-12 flex-shrink-0">
              <img
                v-if="index === 0 || messages[index - 1].sender !== 'bot'"
                class="w-8 h-8 mt-2"
                src="../assets/duckchat3000.png"
                alt="logo"
              />
            </div>
            <div class="bg-gray-100 text-black rounded-lg px-4 py-2 max-w-xs">
              {{ message.text }}
            </div>
          </div>
        </div>

        <div v-else class="flex justify-end mb-1">
          <div class="bg-purple-500 text-white rounded-lg px-4 py-2 max-w-xs ml-auto">
            {{ message.text }}
          </div>
        </div>
      </div>

      <Typing v-if="isTyping" />

      <!-- Options buttons -->
      <div v-if="showOptions" class="flex flex-wrap justify-center gap-2 my-4">
        <button
          v-for="option in options[optionsType]"
          :key="option"
          @click="selectOption(option)"
          class="border border-purple-300 text-purple-500 rounded-full px-4 py-2 hover:bg-purple-100 transition-colors"
        >
          {{ option }}
        </button>
      </div>
    </div>

    <!-- Chat Input -->
    <div class="border-t p-4">
      <input
        v-model="newMessage"
        @keyup.enter="sendMessage"
        type="text"
        placeholder="Reply to DuckChat 3000..."
        class="block w-full border-0 focus:ring-0 outline-none text-gray-600"
      />
      <Footer />
    </div>
  </div>
</template>
