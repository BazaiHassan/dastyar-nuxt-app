<template>
  <UCard v-if="!success">
    <template #header> Sign-in to Assitant App </template>
    <form @submit.prevent="handleLogin">
      <UFormGroup
        label="Email"
        name="email"
        class="mb-4"
        :required="true"
        help="You will recieve an email with the confirmation link!"
      >
        <UInput
          v-model="email"
          type="email"
          placeholder="Email Address"
          required
        ></UInput>
      </UFormGroup>
      <UButton type="submit" variant="solid" color="black" :loading="pending" :disabled="pending"
        >Sign-in</UButton
      >
    </form>
  </UCard>
  <UCard v-else>
    <template #header>Email has been sent</template>
    <div class="text-center">
      <p class="mb-4">
        We have sent an email to <strong>{{ email }}</strong> with a link to
        sign-in.
      </p>

      <p><strong>Important:</strong> The link will be expired in 5 minutes.</p>
    </div>
  </UCard>
</template>

<script setup>
const success = ref(false);
const email = ref("");
const pending = ref(false);
const toast = useToast();
const supabase = useSupabaseClient();

const handleLogin = async () => {
  pending.value = true;
  try {
    const { error } = await supabase.auth.signInWithOtp({
      email: email.value,
      options: {
        emailRedirectTo: "http://localhost:3000",
      },
    });
    if (error) {
      toast.add({
        title: "Error Authentication",
        icon: "i-heroicons-exclamation-circle-20-solid",
        description: error.message,
        color: "red",
      });
    } else {
      success.value = true;
    }
  } finally {
    pending.value = false;
  }
};
</script>

<style scoped></style>
