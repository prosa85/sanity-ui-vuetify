<template>

  <v-card transition="fade-transition" :href="`/blog/${post.slug.current}`">
    <v-img class="mb-2" aspect-ratio="16/9" :src="post.image.asset.url" cover height="225"></v-img>
    <v-card-tile class="text-h5 ma-4 d-block"> {{ post.title }} </v-card-tile>
    <v-card-subtitle>By {{ post.author }}</v-card-subtitle>
    <v-card-text>{{ post.excerpt }}</v-card-text>
  </v-card>

</template>

<script>
import sanity from "../client";
import imageUrlBuilder from "@sanity/image-url";

const imageBuilder = imageUrlBuilder(sanity);

export default {
  name: 'PostCard',
  props: ['post'],
  setup(props) {
    console.log(props.post)
  },
  methods: {
    imageUrlFor(source) {
      return imageBuilder.image(source);
    }

  },
};
</script>

<style scoped></style>