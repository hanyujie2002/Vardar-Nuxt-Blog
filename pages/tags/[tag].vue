<template>
  <main class="mx-auto flex min-h-full w-full flex-col pt-4">
    <ul
      class="mx-auto mb-auto flex w-full max-w-4xl flex-col divide-y divide-dashed"
    >
      <NuxtLink
        :to="`/tags/${currentTag}`"
        class="mb-8 mt-4 px-4 font-serif text-5xl font-extralight tracking-wide animate-in fade-in slide-in-from-bottom sm:max-xl:ml-[10px] dark:text-slate-200"
        >{{ currentTag }}</NuxtLink
      >
      <li v-for="article in articles" :key="article._path" data-aos="fade-in">
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
          <div class="my-auto flex flex-wrap space-x-2">
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
  </main>
</template>

<script setup>
const route = useRoute();
const currentTag = route.params.tag;

const { data: articles } = await useAsyncData(`articles${currentTag}`, () =>
  queryContent('/blog')
    .only(['title', '_path', 'tags', 'date', 'description'])
    .where({ tags: { $icontains: currentTag } })
    .sort({ date: -1 })
    .find()
);
</script>
