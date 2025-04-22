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

<script>
import axios from 'axios'

export default {
  name: 'App',
  data() {
    return {
      username: '',
      phone: '',
      message: '',
      isDisabled: false,
      lastSentTime: 0,
      TELEGRAM_BOT_TOKEN: '7179751378:AAGKQ0vgurJjjzBVsHiybs1PMcuu5PbYMGA',
      TELEGRAM_CHAT_ID: '2043384301',
    }
  },
  methods: {
    async handleSubmit() {
      const now = Date.now()
      if (now - this.lastSentTime < 10000) {
        this.message = 'Please wait 10 seconds before sending another message.'
        return
      }

      this.isDisabled = true

      const text = `📩 Yangi foydalanuvchi:\n👤 Username: ${this.username}\n📱 Phone: ${this.phone}`

      try {
        await axios.post(`https://api.telegram.org/bot${this.TELEGRAM_BOT_TOKEN}/sendMessage`, {
          chat_id: this.TELEGRAM_CHAT_ID,
          text,
        })

        this.message = 'Ma’lumot yuborildi ✅'
        this.lastSentTime = now
      } catch (error) {
        console.error(error)
        this.message = 'Xatolik yuz berdi ❌'
      }

      setTimeout(() => {
        this.isDisabled = false
      }, 10000)
    },
  },
}
</script>

<style scoped>
input:invalid {
  border-color: red;
}
</style>
