<template>
  <div class="sm:w-[calc(100vw-10px)]">
    <nav
      class="fixed top-[--header-height] z-50 h-[calc(100vh-var(--header-height))] w-screen text-slate-600 backdrop-blur-2xl animate-in slide-in-from-right dark:text-themeColor-200"
      :class="{ hidden: isFixedNavHidden }"
    >
      <ul class="mx-auto mt-5 flex w-full flex-col">
        <li class="">
          <NuxtLink
            class="flex px-4 py-2 text-3xl font-bold transition-colors"
            :class="{ active: isAboutPage }"
            to="/admin/about"
            aria-label="About Page"
            @click="hideFixedMenu"
            >{{ $t('about') }}</NuxtLink
          >
        </li>
        <li class="">
          <NuxtLink
            class="flex px-4 py-2 text-3xl font-bold transition-colors"
            :class="{ active: isBlogPage }"
            :to="localePath('/admin/')"
            aria-lable="Blog List Page"
            @click="hideFixedMenu"
            >{{ $t('blog') }}</NuxtLink
          >
        </li>
      </ul>
    </nav>

    <div class="flex w-full flex-col rounded">
      <!-- 导航栏 -->
      <nav
        class="sticky top-0 z-40 flex h-[--header-height] flex-auto text-black backdrop-blur-2xl dark:text-slate-200"
      >
        <ul
          class="mx-auto my-auto flex w-full max-w-screen-xl gap-4"
        >
          <li class="list-item">
            <NuxtLink
              class="flex flex-auto rounded px-4 py-2 md:ml-[10px] text-2xl font-extrabold transition-colors dark:text-slate-100"
              to="/admin"
              title="Home"
              aria-label="Home Page"
              @click="hideFixedMenu"
              >Vardar Blog admin</NuxtLink
            >
          </li>
          <li class="my-auto hidden sm:list-item">
            <NuxtLink
              class="my-auto flex flex-auto rounded px-4 py-2 text-lg font-bold transition-colors hover:text-yellow-500 dark:hover:text-yellow-100"
              :class="{ active: isAboutPage }"
              to="/admin/about"
              aria-label="About"
              >{{ $t('about') }}</NuxtLink
            >
          </li>
          <li class="my-auto hidden sm:list-item">
            <NuxtLink
              class="flex flex-auto rounded px-4 py-2 text-lg font-bold transition-colors hover:text-yellow-500 dark:hover:text-yellow-100"
              :class="{ active: isBlogPage }"
              :to="localePath('/admin/')"
              aria-label="Blog Page"
              >{{ $t('blog') }}</NuxtLink
            >
          </li>
          <li class="relative my-auto ml-auto list-item">
            <button
              title="Search"
              aria-label="Open Search Modal"
              @click="showTokenDialog"
            >
              <Icon
                name="mdi:key-outline"
                class="size-10 transition-colors hover:text-yellow-500 active:text-yellow-400 sm:size-12 dark:hover:text-yellow-100 dark:active:text-yellow-200"
              />
            </button>
            <dialog ref="tokenDialogRef" class="absolute -translate-x-1/2 bg-white rounded">
              <div class="flex flex-col">
              <h2 class="w-36 text-xl font-thin text-center">Set Your Token</h2>
              <div class="mx-2 mt-1">
                <textarea v-model="githubToken" autofocus placeholder="Input Your Github Token" class="w-64 resize-none px-2 py-1 border rounded" />
              </div>
              <div class="flex mx-2 my-1 justify-between">
                <button @click="handleTokenSet" class="bg-green-200 px-3 rounded font-light hover:brightness-95 active:brightness-90">OK</button>
                <button @click="hideTokenDialog" class="bg-red-200 px-3 rounded font-light hover:brightness-95 active:brightness-90">Cancel</button>
              </div>
              </div>
            </dialog>
          </li>
          <li class="sm:max-xl:mr-2 my-auto list-item">
            <ColorModeSwitch />
          </li>
          <li class="my-auto mr-2 sm:hidden">
            <button aria-label="Toggle Menu" @click="toggleFixedMenuState">
              <Icon :name="burgerMenuIconName" class="size-10 sm:size-12" />
            </button>
          </li>
        </ul>
      </nav>

      <!-- 页面主内容 -->
      <main
        class="mx-auto mb-8 flex min-h-[calc(100vh-var(--header-height))] w-full break-words"
      >
        <slot />
      </main>

      <!-- 页脚 -->
      <footer
        class="mx-auto flex w-full max-w-7xl flex-col px-4 py-8 sm:flex-row"
      >
        <div class="order-2 mx-auto mb-2 sm:order-1 sm:mx-0">
          <span class="text-themeColor-300">Copyright © 2024</span>
          <span class="ml-3 text-themeColor-300">Alex Johnson</span>
        </div>
        <div class="order-1 mx-auto sm:order-2 sm:ml-auto sm:mr-0">
          <span class="ml-auto text-slate-700 dark:text-slate-200"
            >Powered by</span
          >
          <NuxtLink
            class="ml-1 text-yellow-400 hover:underline dark:text-yellow-200"
            to="https://github.com/hanyujie2002/Vardar"
            target="_blank"
            >Vardar blog template</NuxtLink
          >
        </div>
      </footer>
    </div>
  </div>
</template>

<script setup lang="ts">
import { debounce } from 'lodash-es';

const route = useRoute();
const isFixedNavHidden = ref<boolean>(true);
const isSearching = ref<boolean>(false);
const isSearchEnded = ref<boolean>(true);
const documentElement = ref();
const burgerMenuIconName = ref<string>('mdi:menu');
const tokenDialogRef = ref();
const githubToken = ref('');

const savedTheme = useCookie('saved_theme');
savedTheme.value = savedTheme.value || '';

const search = ref<string>('');

const results = ref();
const localePath = useLocalePath()

const showTokenDialog = () => {
  tokenDialogRef.value?.show();
}

const hideTokenDialog = () => {
  tokenDialogRef.value?.close()
}

const handleTokenSet = () => {
  localStorage.setItem('github-token', githubToken.value);
  githubToken.value = ''
  hideTokenDialog();
}

const debouncedSearch = debounce(async (newSearch: string) => {
  const searchTimeout = setTimeout(() => {
    if (newSearch !== '') {
      isSearching.value = true;
    }
  }, 100);

  const res = await searchContent(newSearch);
  clearTimeout(searchTimeout);
  results.value = res.value;
  isSearching.value = false;
  isSearchEnded.value = true;
}, 300);

watch(search, async (newSearch: string) => {
  isSearchEnded.value = false;
  debouncedSearch(newSearch);
});

onMounted(() => {
  documentElement.value = document.documentElement;
});
const isAboutPage = computed(() => {
  return route.path.startsWith('/about');
});
const isBlogPage = computed(() => {
  return route.path.startsWith('/blog');
});
const toggleFixedMenuState = () => {
  if (isFixedNavHidden.value === true) {
    documentElement.value.classList.add('overflow-hidden');
    burgerMenuIconName.value = 'mdi:close';
  } else {
    documentElement.value.classList.remove('overflow-hidden');
    burgerMenuIconName.value = 'mdi:menu';
  }

  isFixedNavHidden.value = !isFixedNavHidden.value;
};
const hideFixedMenu = () => {
  isFixedNavHidden.value = true;
  documentElement.value.classList.remove('overflow-hidden');
  burgerMenuIconName.value = 'mdi:menu';
};
</script>

<style scoped>
.active {
  @apply text-yellow-400 hover:text-yellow-400 dark:text-yellow-200 dark:hover:text-yellow-200;
}

@media (min-width: 640px) {
  .custom-max-width {
    max-width: min(48rem, calc(100vw - 2rem));
  }
}
</style>
