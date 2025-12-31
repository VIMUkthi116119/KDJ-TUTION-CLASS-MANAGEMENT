<template>
  <q-page class="flex flex-center bg-dark-page relative-position overflow-hidden">
    <!-- Background Elements -->
    <div class="absolute-full bg-hero-pattern opacity-20"></div>
    <div class="absolute-bottom-right bg-secondary blur-circle opacity-10"></div>
    <div class="absolute-top-left bg-primary blur-circle opacity-10"></div>

    <q-card class="glass-card auth-card q-pa-xl column items-center">
      <div class="row items-center q-mb-lg">
        <q-icon name="school" color="secondary" size="32px" />
        <div class="text-h5 text-weight-bold q-ml-sm text-white font-brand">
          Class<span class="text-secondary">Master</span>
        </div>
      </div>

      <div class="text-h5 text-weight-bold text-white q-mb-sm self-start">Create Account</div>
      <div class="text-grey-5 q-mb-lg self-start">Start managing your institute today</div>

      <q-form @submit="handleRegister" class="full-width q-gutter-y-md">
        <q-input
          v-model="name"
          label="Full Name"
          color="secondary"
          dark
          outlined
          rounded
          lazy-rules
          :rules="[(val) => !!val || 'Name is required']"
        >
          <template v-slot:prepend>
            <q-icon name="person" color="grey-5" />
          </template>
        </q-input>

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
          :rules="[
            (val) => !!val || 'Password is required',
            (val) => val.length >= 6 || 'Password must be at least 6 characters',
          ]"
        >
          <template v-slot:prepend>
            <q-icon name="lock" color="grey-5" />
          </template>
        </q-input>

        <q-input
          v-model="confirmPassword"
          label="Confirm Password"
          type="password"
          color="secondary"
          dark
          outlined
          rounded
          lazy-rules
          :rules="[
            (val) => !!val || 'Please confirm your password',
            (val) => val === password || 'Passwords do not match',
          ]"
        >
          <template v-slot:prepend>
            <q-icon name="lock_clock" color="grey-5" />
          </template>
        </q-input>

        <q-btn
          type="submit"
          color="secondary"
          text-color="dark"
          label="Create Account"
          class="full-width q-py-md text-weight-bold text-subtitle1 shadow-10 q-mt-md"
          rounded
          unelevated
          :loading="loading"
        />
      </q-form>

      <div class="q-mt-xl text-grey-5">
        Already have an account?
        <router-link
          to="/login"
          class="text-secondary text-weight-bold no-decoration hover-underline"
        >
          Sign In
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

const name = ref('')
const email = ref('')
const password = ref('')
const confirmPassword = ref('')
const loading = ref(false)

const handleRegister = async () => {
  loading.value = true
  try {
    const { error } = await supabase.auth.signUp({
      email: email.value,
      password: password.value,
      options: {
        data: {
          full_name: name.value,
        },
      },
    })

    if (error) throw error

    $q.notify({
      type: 'positive',
      message: 'Registration successful! Please check your email to confirm.',
      position: 'top',
      timeout: 5000,
    })

    // Optional: Redirect to login or show instruction
    router.push('/login')
  } catch (error) {
    $q.notify({
      type: 'negative',
      message: error.message || 'Failed to register',
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

.font-brand {
  letter-spacing: -0.05em;
}
</style>
