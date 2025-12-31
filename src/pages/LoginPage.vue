<template>
  <q-page class="flex flex-center bg-dark-page relative-position overflow-hidden">
    <!-- Background Elements -->
    <div class="absolute-full bg-hero-pattern opacity-20 transition-all"></div>
    <div class="absolute-top-right bg-secondary blur-circle opacity-10"></div>
    <div class="absolute-bottom-left bg-primary blur-circle opacity-10"></div>

    <q-card class="glass-card auth-card q-pa-xl column items-center">
      <div class="row items-center q-mb-xl">
        <q-icon name="school" color="secondary" size="40px" />
        <div class="text-h4 text-weight-bold q-ml-sm text-white font-brand">
          Class<span class="text-secondary">Master</span>
        </div>
      </div>

      <div class="text-h5 text-weight-bold text-white q-mb-sm self-start">Welcome Back</div>
      <div class="text-grey-5 q-mb-lg self-start">Sign in to manage your classes</div>

      <q-form @submit="handleLogin" class="full-width q-gutter-y-md">
        <q-input
          v-model="email"
          label="Email Address"
          color="secondary"
          dark
          outlined
          rounded
          lazy-rules
          :rules="[
            (val) => !!val || 'Email is required',
            (val) => /.+@.+\..+/.test(val) || 'Please enter a valid email',
          ]"
        >
          <template v-slot:prepend>
            <q-icon name="email" color="grey-5" />
          </template>
        </q-input>

        <q-input
          v-model="password"
          label="Password"
          type="password"
          color="secondary"
          dark
          outlined
          rounded
          lazy-rules
          :rules="[(val) => !!val || 'Password is required']"
        >
          <template v-slot:prepend>
            <q-icon name="lock" color="grey-5" />
          </template>
        </q-input>

        <div class="row justify-between items-center full-width q-mb-sm">
          <q-checkbox v-model="rememberMe" label="Remember me" dark size="sm" color="secondary" />
          <a href="#" class="text-secondary text-caption no-decoration hover-underline"
            >Forgot Password?</a
          >
        </div>

        <q-btn
          type="submit"
          color="secondary"
          text-color="dark"
          label="Sign In"
          class="full-width q-py-md text-weight-bold text-subtitle1 shadow-10"
          rounded
          unelevated
          :loading="loading"
        />
      </q-form>

      <div class="q-mt-xl text-grey-5">
        Don't have an account?
        <router-link
          to="/register"
          class="text-secondary text-weight-bold no-decoration hover-underline"
        >
          Sign Up
        </router-link>
      </div>
    </q-card>
  </q-page>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import { useQuasar } from 'quasar'
import { supabase } from 'boot/supabase'

const $q = useQuasar()
const router = useRouter()

const email = ref('')
const password = ref('')
const rememberMe = ref(false)
const loading = ref(false)

const handleLogin = async () => {
  loading.value = true
  try {
    const { error } = await supabase.auth.signInWithPassword({
      email: email.value,
      password: password.value,
    })

    if (error) throw error

    $q.notify({
      type: 'positive',
      message: 'Welcome back!',
      position: 'top',
    })

    router.push('/')
  } catch (error) {
    $q.notify({
      type: 'negative',
      message: error.message || 'Failed to sign in',
      position: 'top',
    })
  } finally {
    loading.value = false
  }
}
</script>

<style lang="scss" scoped>
.blur-circle {
  width: 400px;
  height: 400px;
  border-radius: 50%;
  filter: blur(80px);
  position: absolute;
}

.hover-underline:hover {
  text-decoration: underline;
}

// Adjust font if needed, assuming generic sans for now, or the one from previous context
.font-brand {
  letter-spacing: -0.05em;
}
</style>
