<script setup lang="ts">
import { ref } from 'vue'
import { supabase } from "../supabase"
import router from '../router'

const loading = ref(false)
const email = ref('')
const password = ref('')

async function signInWithEmail() {
  try {
    loading.value = true
    const { data, error } = await supabase.auth.signInWithPassword({
      email: email.value,
      password: password.value,
    })
    if (error) throw error
    const { data: { user } } = await supabase.auth.getUser()
    await router.push({ name: 'Accueil' })
  } catch (error) {
    if (error instanceof Error) {
      alert(error.message)
    }
  } finally {
    loading.value = false
  }

}

async function signOut() {
  try {
    loading.value = true
    const { error } = await supabase.auth.signOut()
    if (error) throw error
  } catch (error) {
    if (error instanceof Error) {
      alert(error.message)
    }
  } finally {
    loading.value = false
  }
}

</script>

<template>
  <h1>Connexion</h1>

  <button @click="signOut">Logout</button>

  <form class="row flex-center flex" @submit.prevent="signInWithEmail">
    <div class="col-6 form-widget">
      <div>
        <input class="inputField" type="email" placeholder="Your email" v-model="email" />
        <input class="inputField" type="password" placeholder="Your password" v-model="password" />
      </div>
      <div>
        <button
            type="submit"
            class="button block"
            :value="loading ? 'Loading' : 'Connexion'"
            :disabled="loading"
        >Connexion</button>
      </div>
    </div>
  </form>


</template>

<style scoped>

</style>
