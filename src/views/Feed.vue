<template>

  <n-h1>News Feed</n-h1>
  <n-space justify="center" style="padding-bottom: 2rem;">

    <n-input-group justify="start" >
      <n-input
      style="min-width:250px;max-width:250px;"
      v-model:value="postValue"
      placeholder="Post"
      type="textarea"
      size="medium"
      :autosize="{
        minRows: 1,
        maxRows: 5
      }"
    />
      <n-button type="primary" @click="postItem" ghost>Post</n-button>
  </n-input-group>

  </n-space>

  <n-space justify="center" v-for="post in postList" :key="post.id">
    <n-card size="small">
      <n-space justify="center">
        <n-p>{{post.post}}</n-p>
        <router-link :to="`/user/${post.user}`">
        <n-a>{{ post.profiles.username }}</n-a>
        </router-link>
      </n-space>
    </n-card>
  </n-space>

  <n-space justify="center" v-if="loading">
    <n-spin :show="loading">
    <n-card size="small">
      <n-space justify="center">
        <n-skeleton text :repeat="2" /> <n-skeleton text style="width: 60%;" />
      </n-space>
    </n-card>
    </n-spin>
  </n-space>


   

</template>

<script>
import { ref, onMounted} from "vue";
import { supabase } from "../supabase";
import { store } from "../store";

export default {
  setup() {
    const postList = ref([]);
    const loading = ref(false);
    const postValue = ref("");
    const arr = ref([]);

    async function getPosts() {
      try {
        loading.value = true;
        let { data, error } = await supabase
        .from("posts")
        .select(`* ,profiles(*)`)
        .in('userID', arr.value)
        .order('id', { ascending: false })
        if (data) {
          console.log(data)
          postList.value = data;
        }
      } catch (error) {
        alert(error.message);
      } finally {
        loading.value = false;
      }
    }

    async function getFollowing() {
      try {
        loading.value = true;
        let { data, error } = await supabase
        .from("follows")
        .select(`followedUser`)
        .eq('user', store.user.id)
        if (data) {
       for(let i = 0; i<data.length; i++){
         arr.value.push(data[i].followedUser)
       }
       getPosts();
        }
      } catch (error) {
        alert(error.message);
      } finally {
        loading.value = false;
      }
    }

    async function postItem() {
      try {
        const { data, error } = await supabase
          .from("posts")
          .insert([{ post: postValue.value, userID: store.user.id }]);
      } catch (error) {
        alert(error.message);
      } finally {
        loading.value = false;
        getPosts();
      }
    }

    onMounted(() => {
      getFollowing();
    });

    return {
      loading,
      postValue,
      postList,
      postItem,
      arr,
    };
  },
};
</script>

<style scoped>
.n-card{
min-width: 300px;
margin-left: auto;
margin-right: auto;
}


</style>