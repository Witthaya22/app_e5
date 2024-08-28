<template>
  <div ">
    <v-app class="background-container">
      <nav>
        <!-- Your navigation or additional components can go here -->
      </nav>
      <v-main>
        <NuxtPage />
      </v-main>
    </v-app>
  </div>
</template>

<script>
export default {
  data() {
    return {
      drawer: true
    };
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
  backdrop-filter: blur(10px); /* Apply blur effect */
  -webkit-backdrop-filter: blur(10px); /* For Safari support */
  background: rgba(255, 255, 255, 0.6); /* Semi-transparent white background for contrast */
}

v-app {
  height: 100%;
  display: flex;
  flex-direction: column;
}

v-main {
  flex: 1;
}

</style>
