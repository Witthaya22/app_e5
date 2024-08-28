<template>
      <div class="background-container">
        <!-- Logo Image -->
        <v-img
      class="mx-auto my-6"
      max-width="228"
      max-height="128"
      src="../assets/images/farmer.png"
    ></v-img>

    <!-- Sign Up Card -->
    <v-card
    class="mx-auto pa-12 pb-8 blurred-background"
    elevation="8"
    max-width="448"
    rounded="lg"
    >
    <h1>สมัครสมาชิก</h1>
          <div class="text-subtitle-1 text-medium-emphasis">ชื่อผู้ใช้</div>
  
          <!-- Username Field -->
          <v-text-field
            v-model="username"
            density="compact"
            placeholder="ชื่อผู้ใช้"
            prepend-inner-icon="mdi-account-outline"
            variant="outlined"
          ></v-text-field>
  
          <!-- Email Field -->
          <div class="text-subtitle-1 text-medium-emphasis">อีเมล</div>
          <v-text-field
            v-model="email"
            density="compact"
            placeholder="อีเมลของคุณ"
            prepend-inner-icon="mdi-email-outline"
            variant="outlined"
          ></v-text-field>
  
          <!-- Password Field -->
          <div class="text-subtitle-1 text-medium-emphasis">รหัสผ่าน</div>
          <v-text-field
            v-model="password"
            :append-inner-icon="passwordType === 'password' ? 'mdi-eye' : 'mdi-eye-off'"
            :type="passwordType"
            density="compact"
            placeholder="รหัสผ่านของคุณ"
            prepend-inner-icon="mdi-lock-outline"
            variant="outlined"
            @click:append-inner="togglePasswordVisibility"
          ></v-text-field>
  
          <!-- Success/Error Alerts -->
          <v-alert v-if="error" type="error" class="mt-4">{{ error }}</v-alert>
          <v-alert v-if="success" type="success" class="mt-4">{{ success }}</v-alert>
  
          <!-- Sign Up Button -->
          <v-btn @click="register" class="mb-8"  size="large" variant="tonal" block>
            สมัครสมาชิก
          </v-btn>
  
          <!-- Redirect to Login -->
          <v-card-text class="text-center">
            <a
              class="text-blue text-decoration-none"
              href="/login"
              rel="noopener noreferrer"
            >
              มีบัญชีอยู่แล้ว <v-icon icon="mdi-chevron-right"></v-icon>
            </a>
          </v-card-text>
        </v-card>
      </div>
  </template>
  
  <script setup>

definePageMeta({
  layout: "false",
});
  import { ref } from 'vue';
  import axios from 'axios';
  import { useRouter } from 'vue-router';
  
  const router = useRouter();
  const username = ref('');
  const email = ref('');
  const password = ref('');
  const error = ref('');
  const success = ref('');
  const passwordType = ref('password');
  
  const togglePasswordVisibility = () => {
    passwordType.value = passwordType.value === 'password' ? 'text' : 'password';
  };
  
  const register = async () => {
    try {
      const response = await axios.post('http://localhost:7000/insert', {
        username: username.value,
        password: password.value,
        email: email.value
      });
      if (response.data.ok) {
        success.value = 'Registration successful!';
        error.value = '';
        // Redirect to login page after successful registration
    
          router.push('/login');
 // Delay redirect by 1 second to allow user to see the success message
      } else {
        throw new Error('Registration failed');
      }
    } catch (err) {
      console.error('Registration error:', err);
      success.value = '';
      error.value = 'Registration failed. Please try again.';
    }
  };
  </script>
  
  <style scoped>
 .background-container {
  min-height: 100vh;
  background-image: url('../assets/images/farm3.jpg');
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  overflow: hidden;
  position: relative; /* Required for absolute positioning of the overlay */
}

.blurred-background {
  backdrop-filter: blur(20px); /* Apply blur effect */
  -webkit-backdrop-filter: blur(20px); /* For Safari support */
  background: rgba(255, 255, 255, 0.6); /* Semi-transparent white background for contrast */
}
  </style>
  