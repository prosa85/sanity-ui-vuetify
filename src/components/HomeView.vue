<template>
  <h1>Welcome Altruist Sanity Blog</h1>
  <div v-if="error" class="error">
    {{ error }}
  </div>
  <v-container class="fill-height">
    <v-row>
      <div class="loading" v-if="loading">Loading...</div>
      <v-col cols="6" v-for="post in posts[page]" class="" :key="post._id">
        <PostCard :post="post" />
      </v-col>
    </v-row>
    <v-row justify="center">
      <v-col cols="8">
        <v-container class="max-width">
          <v-bottom-navigation v-model="value" color="primary" active>
            <v-btn :disabled="page == 1" @click="paginationBack()">
              <v-icon>mdi-arrow-left</v-icon>
            </v-btn>
            <v-btn disabled>
              {{ page }}
            </v-btn>
            <v-btn @click="paginationForward()" :disabled="page >= total || loading">
              <v-icon>mdi-arrow-right</v-icon>
            </v-btn>
          </v-bottom-navigation>
        </v-container>
      </v-col>
    </v-row>
  </v-container>

</template>

<script>
import sanity from "../client";

const countTotal = `count(*[_type =='post'])`;
const perPage = 4;
const firstQuery = `*[_type == "post"] | order(_id) [0..${perPage - 1}]{
  _id,
  title,
  "image": mainImage{
  asset->{
  _id,
  url
}
},
  slug,
  excerpt,
  "author":author->name
}`;


export default {
  name: "HomeView",
  data() {
    return {
      loading: true,
      posts: [],
      page: 1,
      total: 0,
      numberPosts: 0,
    };
  },
  created() {
    this.fetchData(firstQuery);
    sanity.fetch(countTotal).then((count) => {
      this.numberPosts = count;
      this.total = Math.ceil(count / perPage);
    }, (error) => {
      console.log("error pulling count");
    });
  },
  watch: {},
  methods: {
    paginationBack() {
      this.page = this.page - 1;
    },
    paginationForward() {
      this.fetchNextPosts();
    },
    fetchData(query) {
      this.error = this.post = null;
      this.loading = true;
      sanity.fetch(query).then((posts) => {
        this.loading = false;
        this.posts[this.page] = posts;
      }, (error) => {
        this.error = error;
      });
    },
    fetchNextPosts() {
      var lastId = "";
      lastId = this.posts[this.page][this.posts[this.page].length - 1]._id;
      this.page = this.page + 1;
      if (!lastId) {
        return [];
      }
      const nextQuery = `*[_type == "post" && _id > "${lastId}"] | order(_id) [0..${perPage - 1}]{
          _id,
          title,
          "image": mainImage{
            asset->{
              _id,
              url
            }
          },
          slug,
          excerpt,
          "author":author->name
          }`;
      this.fetchData(nextQuery);
    }
  },

};
</script>
