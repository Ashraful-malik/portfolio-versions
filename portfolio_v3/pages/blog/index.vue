<!-- ./pages/blog/index.vue -->

<script setup>
definePageMeta({
  key: (route) => route.fullPath,
});

// get tag query
const {
  query: { tags },
} = useRoute();

const filter = ref(tags?.split(","));

// set meta for page
useHead({
  title: "All Blog Posts",
  meta: [{ name: "description", content: "Here's a list of all my Blogs" }],
});
</script>
<template>
  <main>
    <header class="page-heading">
      <div class="wrapper">
        <h1 class="text-5xl font-extrabold">All Blog Posts</h1>
        <p class="font-medium text-lg">Here's a list of all my blog posts</p>
      </div>
    </header>
    <section class="page-section">
      <Tags />

      <!-- Render list of all articles in ./content/blog using `path` -->
      <!-- Provide only defined fieldsin the `:query` prop -->
      <ContentList
        path="/blog"
        :query="{
          only: ['title', 'description', 'tags', '_path', 'img'],
          sort: { date: -1 },
          where: {
            tags: {
              $contains: filter,
            },
          },
          $sensitivity: 'base',
        }"
      >
        <!-- Default list slot -->
        <template v-slot="{ list }">
          <ul class="article-list mb-16 mt-8">
            <li
              v-for="article in list"
              :key="article._path"
              class="article-item"
            >
              <NuxtLink :to="article._path">
                <div class="wrapper">
                  <div class="img-cont w-36 shrink-0">
                    <nuxt-img
                      :src="`/blogImages/${article.img}`"
                      :alt="article.title"
                      class="rounded-lg max-h-[8rem]"
                      format="webp"
                      loading="lazy"
                      quality="95"
                      width="144px"
                      height="120px"
                    />
                  </div>
                  <header>
                    <h1 class="text-2xl font-semibold">{{ article.title }}</h1>
                    <p>{{ article.description }}</p>

                    <ul class="article-tags flex flex-wrap">
                      <li
                        class="tag !py-0.5"
                        v-for="(tag, n) in article.tags"
                        :key="n"
                      >
                        {{ tag }}
                      </li>
                    </ul>
                  </header>
                </div>
              </NuxtLink>
            </li>
          </ul>
        </template>

        <!-- Not found slot to display message when no content us is found -->
        <template #not-found>
          <p>No articles found.</p>
        </template>
      </ContentList>
    </section>
  </main>
</template>
