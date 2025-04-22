<template>
  <div class="p-4 max-w-md mx-auto space-y-4">
    <h1 class="text-xl font-bold">User Info Form</h1>
    <form @submit.prevent="handleSubmit" class="space-y-4">
      <div>
        <label class="block">Username:</label>
        <input v-model="username" type="text" required class="w-full p-2 border rounded" />
      </div>
      <div>
        <label class="block">Phone Number:</label>
        <input
          v-model="phone"
          type="tel"
          required
          pattern="^\+998\d{9}$"
          placeholder="+998901234567"
          class="w-full p-2 border rounded"
        />
      </div>
      <button
        type="submit"
        :disabled="isDisabled"
        class="bg-blue-600 text-white py-2 px-4 rounded disabled:bg-gray-400"
      >
        {{ isDisabled ? 'Please wait...' : 'Submit' }}
      </button>
    </form>
    <p v-if="message">{{ message }}</p>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import axios from 'axios'

const username = ref('')
const phone = ref('')
const message = ref('')
const isDisabled = ref(false)
const lastSentTime = ref(0)

const TELEGRAM_BOT_TOKEN = '7179751378:AAGKQ0vgurJjjzBVsHiybs1PMcuu5PbYMGA'
const TELEGRAM_CHAT_ID = '2043384301'

const handleSubmit = async () => {
  const now = Date.now()
  if (now - lastSentTime.value < 10000) {
    message.value = 'Please wait 10 seconds before sending another message.'
    return
  }

  isDisabled.value = true

  try {
    const text = `📩 Yangi foydalanuvchi:\n👤 Username: ${username.value}\n📱 Phone: ${phone.value}`

    await axios.post(`https://api.telegram.org/bot${TELEGRAM_BOT_TOKEN}/sendMessage`, {
      chat_id: TELEGRAM_CHAT_ID,
      text,
    })

    message.value = 'Ma’lumot yuborildi ✅'
    lastSentTime.value = now
  } catch (err) {
    console.error(err)
    message.value = 'Xatolik yuz berdi ❌'
  }

  setTimeout(() => {
    isDisabled.value = false
  }, 10000)
}
</script>

<style scoped>
input:invalid {
  border-color: red;
}
</style>
