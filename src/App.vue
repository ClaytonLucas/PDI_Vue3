<template>
  <img alt="Vue logo" src="./assets/logo.png" />

  <ul>
    <li v-for="post in posts.data" :key="post.id">
      <span class="title">{{ post.title }}</span>
      <button @click="updateCache(post.id)" class="button">Excluir</button>
    </li>
  </ul>
</template>

<script>
//import HelloWorld from "./components/HelloWorld.vue";

import {useQuery, useResult, useMutation} from "@vue/apollo-composable";
//import GetQuestionByCourseId from "./graphql/GetQuestionByCourseId.gql";
import AllPosts from "./graphql/GetAllPosts.gql";
import DeletePost from "./graphql/DeletePost.gql";
export default {
  name: "App",
  components: {},
  setup() {
    //const {result} = useQuery(GetQuestionByCourseId, {first: 10, offset: 0});
    const options = {
      paginate: {
        page: 1,
        limit: 10,
      },
    };
    const {result} = useQuery(AllPosts, {options: options});
    const posts = useResult(result, null, (data) => data.posts);
    const {mutate: deletePost} = useMutation(DeletePost);

    function updateCache(id) {
      deletePost(
        {id},
        {
          update: (store) => {
            const data = store.readQuery({query: AllPosts});
            const updatedData = data.posts.data.filter((w) => w.id !== id);
            store.writeQuery({query: AllPosts, data: {posts: updatedData}});
          },
        }
      );
      // TODO update the cache
    }

    return {posts, updateCache};
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
ul {
  list-style-type: none;
  background-color: lightgray;
  padding: 20px;
  display: flex;
  flex-direction: column;
  border-radius: 12px;
}
li {
  margin: 15px;
  padding: 20px;
  background-color: lightblue;
  box-shadow: rgba(100, 100, 111, 0.2) 0px 7px 29px 0px;
}
li:hover {
  box-shadow: rgba(0, 0, 0, 0.24) 0px 3px 8px;
}
.button {
  border-radius: 8px;
}
.title {
  margin: 50px;
}
</style>
