<template>
  <n-h1>{{ user.username }}</n-h1>
  <n-button v-if="!followed && store.user.id != user.id" @click="follow">Follow User</n-button>
  <n-button v-else @click="unfollow">Unfollow</n-button>

  <div v-for="post in postList" :key="post.id">
    <n-p>{{ post.post }}</n-p>
  </div>
</template>

<script>
import { supabase } from "../supabase";
import { useRoute } from "vue-router";
import { onMounted, ref } from "vue";
import { store } from "../store";

export default {
  setup() {
    const route = useRoute();
    const user = ref("");
    const postList = ref([]);
    const loading = ref(true);
    store.user = supabase.auth.user();
    const followed = ref(false);
    const following = ref({});
    const followId = ref("");

    console.log(store.user.id)
    console.log(user.value)

    async function getUser() {
      const { data, error } = await supabase
        .from("profiles")
        .select("*")
        .eq("id", route.params.id)
        .single();
      if (error) {
        console.error(error);
      } else {
        user.value = data;
      }
    }

    async function getPosts() {
      try {
        loading.value = true;
        console.log("fetching posts");
        let { data, error } = await supabase
          .from("posts")
          .select("*")
          .eq("userID", route.params.id);
        console.log(data);
        if (data) {
          postList.value = data;
        }
      } catch (error) {
        alert(error.message);
      } finally {
        loading.value = false;
      }
    }

    async function follow() {
      try{
      const { data, error } = await supabase
        .from("follows")
        .insert([{ user: store.user.id, followedUser: user.value.id }]);
        followed.value = true
      } catch (error) {
        alert(error.message);
      } finally {
        loading.value = false;
      }
    }

    async function unfollow() {
      try{
      const { data, error } = await supabase
        .from("follows")
        .delete()
        .eq("id",followId.value)
        followed.value = false;
      } catch (error) {
        alert(error.message);
      } finally {
        loading.value = false;
      }
    }

    async function checkFollowed() {
    const { data, error } = await supabase
        .from("follows")
        .select("*")
        //.eq("user", store.user.id)
        .match({user: store.user.id, followedUser: route.params.id})
        .single()
      if(data){
        following.value = data
        followId.value = following.value.id
        followed.value = true;
      } 
      if (error) {
        console.error(error);
      } else {

      }
    }
    

    onMounted(() => {
      checkFollowed();
      getUser();
      getPosts();
    })

    return {
      user,
      postList,
      follow,
      store,
      followed,
      following,
      unfollow,
      followId
    };
  },
};
</script>