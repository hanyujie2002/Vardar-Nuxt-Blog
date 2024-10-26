<template>
  <div class="w-[calc(100vw-10px)] dark:text-slate-300 break-all">
    <div class="max-w-4xl px-4 mx-auto table w-full">
      <div class="table-header-group">
        <div class="table-row backdrop-brightness-90 rounded">
          <div class="table-cell"><div class="px-2 py-2">Title</div></div>
          <div class="table-cell"><div class="px-2 py-2">Date</div></div>
          <div class="table-cell"><div class="px-2 py-2">Operate</div></div>
        </div>
      </div>

      <div class="table-row-group">
        <div class="table-row transition-colors rounded hover:backdrop-brightness-95" v-for="file in sortedFiles">
          <div class="table-cell"><div class="px-2 py-2">{{ file.content.data.title }}</div></div>
          <div class="table-cell"><div class="px-2 py-2">{{ new Date(file.content.data.date).toLocaleDateString(undefined, { year: 'numeric', month: 'short', day: 'numeric' }) }}</div></div>
          <div class="table-cell"><div class="px-2 py-2"><NuxtLink class="hover:text-themeColor-500 dark:hover:text-themeColor-600" :to="getApiPath(file.path)">Edit</NuxtLink></div></div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
definePageMeta({
  layout: 'admin'
})

import { ref, onMounted } from 'vue'
import { parseMarkdown } from '@nuxtjs/mdc/runtime'
const inputText = ref('# loading...')

const files = ref([])

onMounted(async () => {
  const repoUrl = 'https://api.github.com/repos/hanyujie2002/Vardar-Nuxt-Blog/contents/content/blog'
  const token = localStorage.getItem('github-token')

  const response = await fetch(repoUrl, {
    headers: {
      'Authorization': `token ${token}`,
    }
  })
  const fileList = await response.json()

  for (const file of fileList) {
    const fileResponse = await fetch(file.git_url, {
      headers: {
        'Authorization': `token ${token}`,
        'Accept': 'application/vnd.github.v3.raw',
      }
    })

    const fileContent = await fileResponse.text()

    const ast = await parseMarkdown(fileContent)
    file.content = ast;
    files.value.push(file)
  }
})

const sortedFiles = computed(() => {
  return files.value.sort((a, b) => new Date(b.content.data.date) - new Date(a.content.data.date))
})

const getApiPath = (path) => {
  return '/admin' + path.replace(/^content/, '').replace(/\.md$/, '');
}
</script>
