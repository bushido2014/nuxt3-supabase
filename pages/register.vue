<template>
  <div class="py-8">
    <h2 class="register__title text-center">Register</h2>
    <div v-if="showError" class="max-w register__error-msg">
      <p>{{ errorMsg }}</p>
    </div>
    <form class="form max-w" @submit.prevent="registerUser">
      <div class="form__group">
        <label class="form__label">Email</label>
        <input v-model="email" type="text" class="form__input" />
      </div>
      <div class="form__group">
        <label class="form__label">Password</label>
        <input v-model="password" type="password" class="form__input" />
      </div>
      <div class="form__group text-center">
        <button :disabled="loading" type="submit" class="form__btn">
          Register
        </button>
      </div>
    </form>
    <div class="max-w text-center account-info">
      <p>
        Already have an account?
        <NuxtLink class="account-info__link" to="/login">Log in here</NuxtLink>
      </p>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ApiError } from '@supabase/gotrue-js';
const client = useSupabaseClient();
const user = useSupabaseUser();
const email = ref('');
const password = ref('');
const loading = ref(false);
const showError = ref(false);
const errorMsg = ref('');
const hideError = () => {
  showError.value = false;
};
const registerUser = async () => {
  try {
    showError.value = false;
    loading.value = true;
    const { session, error } = await client.auth.signUp({
      email: email.value,
      password: password.value,
    });
    if (error) throw error;
    // Make sure the session is set before the `auth` middleware runs
    await client.auth.setSession(session.refresh_token);
    navigateTo('/profile');
  } catch (e) {
    const error = e as ApiError;
    showError.value = true;
    errorMsg.value = error.message;
    setTimeout(hideError, 3000);
  } finally {
    loading.value = false;
  }
};

useHead({
  title: 'Register Page',
  meta: [{ name: 'description', content: 'Register Page' }],
  bodyAttrs: {
    class: 'register-page',
  },
});
</script>
