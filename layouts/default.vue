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
            to="/about"
            aria-label="About Page"
            @click="hideFixedMenu"
            >{{ $t('about') }}</NuxtLink
          >
        </li>
        <li class="">
          <NuxtLink
            class="flex px-4 py-2 text-3xl font-bold transition-colors"
            :class="{ active: isBlogPage }"
            :to="localePath('/blog/page/1')"
            aria-lable="Blog List Page"
            @click="hideFixedMenu"
            >{{ $t('blog') }}</NuxtLink
          >
        </li>
        <li class="">
          <NuxtLink
            class="flex px-4 py-2 text-3xl font-bold transition-colors"
            :class="{ active: isTagsPage }"
            :to="localePath('/tags')"
            aria-lable="Tags List Page"
            @click="hideFixedMenu"
            >{{ $t('tags') }}</NuxtLink
          >
        </li>
        <li>
          <a
            class="flex px-4 py-2 text-3xl font-bold transition-colors"
            target="_blank"
            to="/feed.xml"
            title="RSS Feed"
            aria-label="RSS Feed"
            @click="hideFixedMenu"
          >
            RSS
          </a>
        </li>
      </ul>
    </nav>

    <div class="flex w-full flex-col rounded">
      <!-- 导航栏 -->
      <nav
        class="sticky top-0 z-40 flex h-[--header-height] flex-auto text-black backdrop-blur-2xl dark:text-slate-200"
      >
        <ul class="mx-auto my-auto flex w-full max-w-screen-xl gap-4">
          <li class="list-item">
            <NuxtLink
              class="flex flex-auto rounded px-4 py-2 text-2xl font-extrabold transition-colors md:ml-[10px] dark:text-slate-100"
              to="/"
              title="Home"
              aria-label="Home Page"
              @click="hideFixedMenu"
              >Vardar Blog</NuxtLink
            >
          </li>
          <li class="my-auto hidden sm:list-item">
            <NuxtLink
              class="my-auto flex flex-auto rounded px-4 py-2 text-lg font-bold transition-colors hover:text-yellow-500 dark:hover:text-yellow-100"
              :class="{ active: isAboutPage }"
              to="/about"
              aria-label="About"
              >{{ $t('about') }}</NuxtLink
            >
          </li>
          <li class="my-auto hidden sm:list-item">
            <NuxtLink
              class="flex flex-auto rounded px-4 py-2 text-lg font-bold transition-colors hover:text-yellow-500 dark:hover:text-yellow-100"
              :class="{ active: isBlogPage }"
              :to="localePath('/blog/page/1')"
              aria-label="Blog Page"
              >{{ $t('blog') }}</NuxtLink
            >
          </li>
          <li class="my-auto hidden sm:list-item">
            <NuxtLink
              class="flex flex-auto rounded px-4 py-2 text-lg font-bold transition-colors hover:text-yellow-500 dark:hover:text-yellow-100"
              :class="{ active: isTagsPage }"
              :to="localePath('/tags')"
              aria-label="Tags List Page"
              >{{ $t('tags') }}</NuxtLink
            >
          </li>
          <li class="my-auto ml-auto list-item">
            <button
              title="Search"
              aria-label="Open Search Modal"
              @click="showSearchModal"
            >
              <Icon
                name="mdi:search"
                class="size-10 transition-colors hover:text-yellow-500 active:text-yellow-400 sm:size-12 dark:hover:text-yellow-100 dark:active:text-yellow-200"
              />
            </button>
          </li>
          <li class="my-auto list-item">
            <ColorModeSwitch />
          </li>
          <li class="my-auto hidden sm:list-item">
            <NuxtLink
              target="_blank"
              to="/feed.xml"
              title="RSS"
              aria-label="RSS Feed"
            >
              <Icon
                name="mdi:rss"
                class="size:5 transition-colors hover:text-yellow-500 active:text-yellow-400 sm:size-12 dark:hover:text-yellow-100 dark:active:text-yellow-200"
              />
            </NuxtLink>
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

    <!-- 搜索框 -->
    <dialog
      ref="dialogRef"
      class="custom-max-width h-dvh max-h-none w-screen max-w-none bg-white shadow-lg shadow-themeColor-100 animate-in slide-in-from-right backdrop:backdrop-blur-sm sm:h-[28rem] sm:w-full sm:bg-white sm:zoom-in sm:slide-in-from-right-0 dark:bg-themeColor-950 dark:shadow-themeColor-900 dark:sm:bg-themeColor-950"
      @click="handleBackdropClicked"
      @keydown="handleEscKeyDown"
    >
      <div
        class="flex h-full w-full flex-col text-slate-700 dark:text-slate-100"
      >
        <div
          class="relative flex items-center border-b border-themeColor-300/50 dark:border-themeColor-200/30"
        >
          <Icon
            :name="isSearching ? 'svg-spinners:clock' : 'mdi:search'"
            class="m-3 flex h-10 w-10 sm:h-5 sm:w-5"
          />
          <input
            v-model="search"
            placeholder="search blog"
            class="flex h-[--header-height] flex-grow bg-transparent text-xl placeholder:text-slate-800/50 focus:outline-none sm:h-12 sm:text-base dark:placeholder:text-slate-200/50"
          >
          <button class="group" @click="hideSearchModal">
            <Icon
              name="mdi:close"
              class="m-3 flex h-10 w-10 group-hover:text-yellow-500 sm:h-5 sm:w-5 dark:group-hover:text-yellow-300"
            />
          </button>
        </div>
        <div
          v-if="search.length"
          class="flex flex-grow flex-col overflow-y-auto scrollbar:h-1.5 scrollbar:w-1.5 scrollbar-track:rounded scrollbar-track:bg-slate-100 scrollbar-thumb:rounded scrollbar-thumb:bg-slate-300 dark:scrollbar-track:bg-slate-500/[0.16] dark:scrollbar-thumb:bg-slate-500/50"
        >
          <div v-if="results?.length > 0">
            <div
              v-for="result in results"
              :key="result.id"
              class="border-b border-themeColor-200 dark:border-themeColor-200/30"
            >
              <NuxtLink
                class="flex flex-col p-4 hover:backdrop-brightness-95 active:backdrop-brightness-90 dark:hover:backdrop-brightness-105 dark:active:backdrop-brightness-110"
                :to="result.id"
                @click="hideSearchModal"
              >
                <h3
                  class="result.title line-clamp-2 text-lg font-semibold text-slate-800 dark:text-slate-100"
                >
                  <span
                    v-if="result.titles.length"
                    class="after:content-['_>_']"
                  >
                    {{ result.titles[0] }}
                  </span>
                  <span>
                    {{ result.title }}
                  </span>
                </h3>
                <p
                  class="line-clamp-2 text-sm text-slate-400 dark:text-slate-200"
                >
                  {{ result.content }}
                </p>
              </NuxtLink>
            </div>
          </div>
          <div
            v-else-if="results?.length === 0 && isSearchEnded"
            class="flex flex-grow items-center justify-center"
          >
            <span class="flex flex-grow justify-center text-center text-5xl">{{
              $t('noResult')
            }}</span>
          </div>
        </div>
        <div v-else class="flex flex-grow items-center justify-center">
          <span class="flex flex-grow justify-center text-center text-5xl">{{
            $t('typeToSearch')
          }}</span>
        </div>
      </div>
    </dialog>
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

const savedTheme = useCookie('saved_theme');
savedTheme.value = savedTheme.value || '';

const search = ref<string>('');
const dialogRef = ref<HTMLDialogElement>();

const results = ref();
const localePath = useLocalePath();

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
const isTagsPage = computed(() => {
  return route.path.startsWith('/tags');
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
const showSearchModal = async () => {
  documentElement.value.classList.add('overflow-hidden');
  isFixedNavHidden.value = true;
  burgerMenuIconName.value = 'mdi:menu';

  dialogRef.value?.showModal();
};
const hideSearchModal = async () => {
  documentElement.value.classList.remove('overflow-hidden');
  search.value = '';

  dialogRef.value?.close();
};

const handleBackdropClicked = (event: MouseEvent) => {
  if (event.target == dialogRef.value) {
    hideSearchModal();
  }
};

const handleEscKeyDown = (event: KeyboardEvent) => {
  if (event.key === 'Escape') {
    event.preventDefault();
    hideSearchModal();
  }
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
