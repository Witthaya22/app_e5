<template>
 <div >

  <v-app>
    <v-card class="blurred-background">
      <v-layout>
        <!-- Navigation Drawer -->
        <v-navigation-drawer
          v-model="drawer"
          :rail="rail"
          permanent
          @click="rail = false"
        >
          <v-list-item
            prepend-avatar="\assets\images\kruoak.jpg"
            title="Witthaya"
            nav
          >
            <template v-slot:append>
              <v-btn
                icon="mdi-chevron-left"
                variant="text"
                @click.stop="rail = !rail"
              ></v-btn>
            </template>
          </v-list-item>
          <v-divider></v-divider>
          <v-list density="compact" nav>
            <v-list-item
              prepend-icon="mdi-home"
              title="หน้าหลัก"
              value="home"
              @click="main"
            ></v-list-item>
            <v-list-item
              prepend-icon="mdi-view-dashboard"
              title="Dashboard"
              value="dashboard"
              @click="dashboard"
            ></v-list-item>
            <v-list-item
              prepend-icon="mdi-database"
              title="แสดงข้อมูล"
              value="showdata"
              @click="showdata"
            ></v-list-item>
            <v-list-item
              prepend-icon="mdi-email"
              title="ติดต่อ"
              value="contact"
              @click="contact"
            ></v-list-item>
            <v-list-item
              prepend-icon="mdi-information"
              title="เกี่ยวกับ"
              value="about"
              @click="about"
            ></v-list-item>
            <v-list-item
              prepend-icon="mdi-logout"
              title="LOGOUT"
              value="logout"
              @click="logOut"
            ></v-list-item>
          </v-list>
        </v-navigation-drawer>
        <!-- Main Content -->
        <v-main class="background-container">
          <NuxtPage></NuxtPage>
        </v-main>
      </v-layout>
    </v-card>
  </v-app>
</div>
</template>

<script>
export default {
  data() {
    return {
      drawer: true,
      rail: true,
    };
  },
  methods: {
    main() {
      this.$router.replace("/home");
    },
    showdata() {
      this.$router.replace("/datatable");
    },
    about() {
      this.$router.replace("/about");
    },
    contact() {
      this.$router.replace("/contact");
    },
    dashboard() {
      this.$router.replace("/dashboard");
    },
    logOut() {
      window.sessionStorage.clear(); 
      this.$router.replace("/login");
    },
  },
};
</script>

<style scoped>

</style>
