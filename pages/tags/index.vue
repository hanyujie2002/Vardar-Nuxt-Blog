<template>
  <div class="w-full font-extralight sm:tracking-wide">
    <div
      class="mx-auto mt-8 flex max-w-3xl flex-wrap overflow-x-auto"
      data-aos="fade-up"
    >
      <NuxtLink
        v-for="(count, tag) in tagCounts"
        :key="tag"
        :to="`/tags/${tag}`"
        class="mx-4 my-1.5 space-x-2 rounded-lg border border-themeColor-300/50 p-3 hover:backdrop-brightness-95 active:backdrop-brightness-90 dark:text-white dark:hover:backdrop-brightness-105 dark:active:backdrop-brightness-110"
      >
        <span>{{ tag }}</span> <span>{{ count }}</span>
      </NuxtLink>
    </div>
  </div>
</template>

<script setup>
const { data: tags } = await useAsyncData(
  'tags',
  queryContent('/blog').only('tags').find
);

const tagCounts = tags.value.reduce((acc, tagObj) => {
  tagObj.tags.forEach((tag) => {
    const lowerCasedTag = tag.toLowerCase();
    if (acc[lowerCasedTag]) {
      acc[lowerCasedTag] += 1;
    } else {
      acc[lowerCasedTag] = 1;
    }
  });
  return acc;
}, {});
</script>
