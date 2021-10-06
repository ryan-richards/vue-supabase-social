<template>
<div style="max-width:400px;margin-left:auto;margin-right:auto;">
    <n-card>
    <n-tabs default-value="signin" size="large">
      <n-tab-pane name="signin" tab="Sign in">
        <n-form>
          <n-form-item-row label="Email">
            <n-input type="email" v-model:value="email"/>
          </n-form-item-row>
          <n-form-item-row label="Password">
            <n-input type="password" v-model:value="password" />
          </n-form-item-row>
        </n-form>
        <n-button type="primary" @click="handleLogin" block>Sign In</n-button>
      </n-tab-pane>
      <n-tab-pane name="signup" tab="Sign Up">
        <n-form>
          <n-form-item-row label="Email">
            <n-input type="email" v-model:value="email"/>
          </n-form-item-row>
          <n-form-item-row label="Password">
            <n-input type="password" v-model:value="password" />
          </n-form-item-row>
        </n-form>
        <n-button type="primary" @click="handleSignUp" block>Sign Up</n-button>
      </n-tab-pane>
    </n-tabs>
  </n-card>

</div>



</template>

<script>
import { ref } from "vue"
import { supabase } from "../supabase"
import { useRouter } from "vue-router";


export default {
  setup() {
    const loading = ref(false)
    const email = ref("")
    const password = ref("")
    const router = useRouter()

    const handleLogin = async () => {
      try {
        loading.value = true
        const { error } = await supabase.auth.signIn({ 
            email: email.value,
            password: password.value
            })
        if (error) throw error
        alert("Signed In")
        router.push('/')
      } catch (error) {
        alert(error.error_description || error.message)
      } finally {
        loading.value = false
        email.value = ""
        password.value = ""
      }
    }


    const handleSignUp = async () => {
      try {
        loading.value = true
        const { error } = await supabase.auth.signUp({ 
            email: email.value,
            password: password.value
            })
        if (error) throw error
        alert("Signed Up")
        router.push('/')
      } catch (error) {
        alert(error.error_description || error.message)
      } finally {
        loading.value = false
        email.value = ""
        password.value = ""
      }
    }



    return {
      loading,
      email,
      password,
      handleLogin,
      handleSignUp,
    }
  },
}


</script>