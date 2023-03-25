<script setup>
import { Chat } from "@chat-ui/vue3"
import { ref } from "vue";

defineProps({
  msg: {
    type: String,
    required: true
  }
})

const chat = ref([])

const handlerMessage = (msg) => {
  console.log(msg)
  chat.value.push({
    message: msg,
    type: 'person',
    timestamp: '2021/01/01',
  })

  getGPT(msg)
}

const getGPT = async (msg) => {
  const host = window.location.hostname
  console.log(host)
  const response = await fetch(`http://${host}:3000/text/?prompt="${msg}"`)
  const res = await response.json()
  const chatMsg = {
    type: 'chatbot',
    timestamp: (new Date()).getDate(),
    message: res.text,
  }
  chat.value.push(chatMsg)
}
</script>

<template>
  <div class="greetings">
    <h1 class="green">{{ msg }}</h1>
    <h3>
      You’ve successfully created a project with
      <a href="https://vitejs.dev/" target="_blank" rel="noopener">Vite</a> +
      <a href="https://vuejs.org/" target="_blank" rel="noopener">Vue 3</a>.
    </h3>
    <Chat
      :chat="chat"
      :onSend="handlerMessage" />
  </div>
</template>

<style scoped>
h1 {
  font-weight: 500;
  font-size: 2.6rem;
  top: -10px;
}

h3 {
  font-size: 1.2rem;
}

.greetings h1,
.greetings h3 {
  text-align: center;
}

@media (min-width: 1024px) {
  .greetings h1,
  .greetings h3 {
    text-align: left;
  }
}
</style>
