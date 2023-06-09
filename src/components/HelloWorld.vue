<script setup>
import { Chat } from "@chat-ui/vue3"
import { ref } from "vue";
import { Configuration, OpenAIApi } from "openai"

defineProps({
  msg: {
    type: String,
    required: true
  }
})

const chat = ref([])

const handlerMessage = async (msg) => {
  console.log(msg)
  chat.value.push({
    message: msg,
    type: 'person',
    timestamp: formatDate(),
  })

  const res = await getGPT(msg)
  setChatData(res)
}

const getGPT = async (msg) => {
  const configuration = new Configuration({
    organization: import.meta.env.VITE_OPENAI_ORG,
    apiKey: import.meta.env.VITE_OPENAI_KEY,
  })
  const openai = new OpenAIApi(configuration)

  const completion = await openai.createChatCompletion({
    model: 'gpt-3.5-turbo',
    messages: [{
      role: 'user',
      content: msg,
    }],
    max_tokens: 1000,
    temperature: 0.5,
  }).catch((error) => {
    console.log(error)
    return
  })

  return completion.data.choices[0].message.content
}

const setChatData = (msg) => {
  const chatMsg = {
    type: 'chatbot',
    timestamp: formatDate(),
    message: msg,
  }

  chat.value.push(chatMsg)
}

const formatDate = () => {
  const now = new Date()

  const year = now.getFullYear()
  const month = now.getMonth()
  const day = now.getDay()
  const hour = now.getHours()
  const min = now.getMinutes()

  return `${year}/${month}/${day} ${hour}:${min}`
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
