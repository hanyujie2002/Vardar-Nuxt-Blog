<template>
  <div class="flex w-[calc(100vw-10px)] flex-col items-center justify-center">
    <div class="flex w-full max-w-7xl">
      <div class="mx-2 flex w-full rounded-t bg-gray-200">
        <button
          class="my-2 ml-auto mr-2 rounded bg-green-200 px-3 py-2 text-slate-800 hover:brightness-95 active:brightness-90"
          @click="updateFile"
        >
          Update
        </button>
      </div>
    </div>
    <div
      class="grid max-h-[820px] max-w-7xl grid-rows-2 overflow-auto px-2 xl:grid-cols-2 xl:grid-rows-1"
    >
      <MonacoEditor
        v-model="inputText"
        lang="markdown"
        class="max-h-[820px] flex-grow overflow-auto border"
        placeholder="Enter text to render"
      />
      <MDC
        class="max-h-[820px] flex-grow overflow-auto border"
        :value="processedInputText"
      />
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';

definePageMeta({
  layout: 'admin',
});
const inputText = ref('# loading...');
const sha = ref('');
const articleUrl =
  'https://api.github.com/repos/hanyujie2002/Vardar-Nuxt-Blog/contents/content' +
  '/about' +
  '.md?ref=main';
const token = localStorage.getItem('github-token');

onMounted(async () => {
  const response = await fetch(articleUrl, {
    headers: {
      Authorization: `token ${token}`,
    },
    cache: 'no-cache',
  });

  const data = await response.json();
  sha.value = data.sha;

  const base64Content = data.content;
  const decodedContent = new TextDecoder('utf-8').decode(
    Uint8Array.from(atob(base64Content), (c) => c.charCodeAt(0))
  );

  inputText.value = decodedContent;
});

const processedInputText = computed(() =>
  inputText.value.length > 0 ? inputText.value : 'No content yet'
);

const updateFile = async () => {
  const updatedContent = new TextEncoder().encode(inputText.value);
  const base64Content = btoa(
    String.fromCharCode(...new Uint8Array(updatedContent))
  );

  const response = await fetch(articleUrl, {
    method: 'PUT',
    headers: {
      Authorization: `token ${token}`,
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      message: 'Test: Updating blog content',
      content: base64Content,
      sha: sha.value,
    }),
  });

  if (response.ok) {
    console.log('File updated successfully!');
  } else {
    console.error('File update failed', response.statusText);
  }
};
</script>
