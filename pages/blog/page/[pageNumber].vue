<template>
  <main class="mx-auto flex min-h-full w-full flex-col pt-4">
    <ul
      class="mx-auto mb-auto flex w-full max-w-4xl flex-col divide-y divide-dashed"
    >
      <li
        v-for="article in paginatedArticles"
        :key="article._path"
        data-aos="fade-in"
      >
        <div
          class="flex h-44 flex-col rounded px-4 py-2 font-sans font-extralight transition-all hover:backdrop-brightness-95 has-[a:active]:backdrop-brightness-90 sm:tracking-wide sm:max-xl:pl-[26px] dark:text-slate-300 dark:hover:text-slate-200 dark:hover:backdrop-brightness-105 dark:has-[a:active]:backdrop-brightness-110"
        >
          <NuxtLink
            :to="article._path"
            class="line-clamp-2 font-serif text-xl [text-shadow:_0px_1px_1px_rgba(0,0,0,0.1)]"
            >{{ article.title }}</NuxtLink
          >
          <div class="my-auto flex space-x-2">
            <Icon name="mdi:calendar-clock" class="size-6 text-slate-500" />
            <p>
              {{
                new Date(article.date).toLocaleDateString(undefined, {
                  year: 'numeric',
                  month: 'long',
                  day: 'numeric',
                })
              }}
            </p>
          </div>
          <div class="my-auto flex flex-wrap">
            <div
              v-for="tag in article.tags"
              :key="tag"
              class="mr-2 text-blue-400 hover:brightness-75 dark:text-blue-600 dark:hover:brightness-125"
            >
              <NuxtLink :to="`/tags/${tag}`">#{{ tag }}</NuxtLink>
            </div>
          </div>

          <p class="mt-auto line-clamp-2">{{ article.description }}</p>
        </div>
      </li>
    </ul>
    <div class="my-4 flex justify-center">
      <VueAwesomePaginate
        :model-value="currentPage"
        :total-items="totalRecords ?? 0"
        :items-per-page="perPage"
        :max-pages-shown="3"
        :hide-prev-next-when-ends="true"
        type="link"
        link-url="/blog/page/[page]"
        @click="onPageChange"
      />
    </div>
  </main>
</template>

<script setup lang="ts">
import { VueAwesomePaginate } from 'vue-awesome-paginate';

useHead({
  title: 'Article List',
  meta: [{ name: 'description', content: 'Article List' }],
});

const route = useRoute();

const perPage = ref(15);
const currentPage: globalThis.Ref<number> = ref(1);

const page: (string | null) | (string | null)[] = route.params.pageNumber;

if (!(Array.isArray(page) || page === undefined || page === null)) {
  currentPage.value = parseInt(page);
}

const { data: articles } = await useAsyncData('articles', () =>
  queryContent('/blog')
    .only(['title', '_path', 'tags', 'date', 'description'])
    .sort({ date: -1 })
    .find()
);

const totalRecords = computed(() => articles.value?.length);

const paginatedArticles = computed(() => {
  const start = (currentPage.value - 1) * perPage.value;
  const end = start + perPage.value;
  return articles.value?.slice(start, end);
});

const onPageChange = async (page: number) => {
  await navigateTo({
    path: `/blog/page/${page}`,
  });
};
</script>

<style>
.pagination-container {
  @apply flex;
}

.paginate-buttons {
  @apply h-9 w-9 cursor-pointer rounded text-black backdrop-brightness-95 transition-all hover:backdrop-brightness-90 active:backdrop-brightness-75 sm:h-12 sm:w-12 dark:text-white;
}

.active-page {
  @apply backdrop-brightness-75 hover:backdrop-brightness-75;
}

.back-button,
.next-button {
  @apply mx-4;
}

.disabled-paginate-buttons {
  @apply cursor-auto backdrop-brightness-100 hover:cursor-auto hover:backdrop-brightness-100;
}
</style>
