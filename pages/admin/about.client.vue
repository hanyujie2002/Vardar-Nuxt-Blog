<template>
  <div class="flex flex-col w-[calc(100vw-10px)] justify-center items-center">
    <div class="max-w-7xl w-full flex">
      <div class="w-full flex mx-2 bg-gray-200 rounded-t">
        <button @click="updateFile" class="bg-green-200 text-slate-800 hover:brightness-95 active:brightness-90 px-3 py-2 rounded my-2 ml-auto mr-2">Update</button>
      </div>
    </div>
    <div class="max-w-7xl px-2 grid grid-rows-2 xl:grid-rows-1 xl:grid-cols-2 max-h-[820px] overflow-auto">
      <MonacoEditor lang="markdown" class="flex-grow max-h-[820px] border overflow-auto" v-model="inputText"
        placeholder="Enter text to render" />
      <MDC class="flex-grow max-h-[820px] border overflow-auto" :value="processedInputText"></MDC>
    </div>
  </div>
</template>

<script setup>
definePageMeta({
  layout: 'admin'
})

import { ref, onMounted } from 'vue'
const inputText = ref('# loading...')
const route = useRoute()
const path = route.path
const stripedPath = path.replace(/^\/admin/, '');
const sha = ref('')
const articleUrl = 'https://api.github.com/repos/hanyujie2002/Vardar-Nuxt-Blog/contents/content' + '/about' + '.md?ref=main'
const token = localStorage.getItem('github-token')

onMounted(async () => {
  const response = await fetch(articleUrl, {
    headers: {
      'Authorization': `token ${token}`,
    },
    cache: 'no-cache'
  })

  const data = await response.json()
  sha.value = data.sha;

  const base64Content = data.content
  const decodedContent = new TextDecoder('utf-8').decode(Uint8Array.from(atob(base64Content), c => c.charCodeAt(0)))

  inputText.value = decodedContent
})

const processedInputText = computed(() => inputText.value.length > 0 ? inputText.value : 'No content yet');

const updateFile = async () => {
  const updatedContent = new TextEncoder().encode(inputText.value);
  const base64Content = btoa(String.fromCharCode(...new Uint8Array(updatedContent)))

  const response = await fetch(articleUrl, {
    method: 'PUT',
    headers: {
      'Authorization': `token ${token}`,
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      message: 'Test: Updating blog content',
      content: base64Content,
      sha: sha.value,
    })
  })

  if (response.ok) {
    console.log('File updated successfully!')
  } else {
    console.error('File update failed', response.statusText)
  }
}
</script>

