<template>
  <v-container fluid class="fill-height d-flex flex-column align-center justify-center">
      <v-card-title class="d-flex justify-center">
        <h1>เกี่ยวกับฉัน</h1>
      </v-card-title>

      <v-divider class="my-4"></v-divider>

      <v-card-text>
        <v-row>
          <v-col cols="12" md="4">
            <!-- <v-avatar size="200" class="" style="margin: 0 auto"> -->
              <img src="../assets/images/kap.jpg" alt="Profile Picture" width="200" height="200" class="rounded-circle">
            <!-- </v-avatar> -->
          </v-col>
          <v-col cols="12" md="8">
            <h2>{{ name }}</h2>
            <p>
              {{ bio }}
            </p>

            <v-divider class="my-4"></v-divider>

            <h3>ความสามารถ</h3>
            <v-chip-group column>
              <v-chip v-for="skill in skills" :key="skill" class="ma-1" color="primary" text-color="white">
                {{ skill }}
              </v-chip>
            </v-chip-group>

            <v-divider class="my-4"></v-divider>

            <h3>ติดต่อ</h3>
            <v-list>
              <v-list-item>
                <v-list-item-icon>
                  <v-icon>mdi-phone</v-icon>
                </v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title>{{ phone }}</v-list-item-title>
                </v-list-item-content>
              </v-list-item>
              <v-list-item>
                <v-list-item-icon>
                  <v-icon>mdi-email</v-icon>
                </v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title>{{ email }}</v-list-item-title>
                </v-list-item-content>
              </v-list-item>
              <!-- <v-list-item>
                <v-list-item-icon>
                  <v-icon>mdi-linkedin</v-icon>
                </v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title>
                    <a :href="linkedin" target="_blank">{{ linkedin }}</a>
                  </v-list-item-title>
                </v-list-item-content>
              </v-list-item> -->
              <v-list-item>
                <v-list-item-icon>
                  <v-icon>mdi-github</v-icon>
                </v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title>
                    <a :href="github" target="_blank">{{ github }}</a>
                  </v-list-item-title>
                </v-list-item-content>
              </v-list-item>
            </v-list>
          </v-col>
        </v-row>
      </v-card-text>
  </v-container>
</template>

<script>
export default {
  data() {
    return {
      name: "นาย วิทยา เพ็งแจ่ม",
      bio: "นักศึกษา วิทยาลัยเทคนิคชัยภูมิ ปวส.2 สาขาเทคโนโลยีสารสนเทศ จบมาจากวิทยาลัยเทคโนโลยีชุมแพไทย-เยอรมัน ปีการศึกษา 2565",
      skills: ["JavaScript", "Vue.js", "Vuetify", "CSS", "HTML"],
      phone: "+66 123 456 789",
      email: "66309010031@ccollege.ac.th",
      github: "https://github.com/Witthaya22",
    };
  },
};
</script>

<style scoped>
.v-card-title {
  color: #34495e;
  font-weight: bold;
}

.v-avatar img {
  border-radius: 50%;
}

.v-chip {
  font-size: 14px;
}

.v-list-item-title a {
  color: #1976d2;
  text-decoration: none;
}

.v-list-item-title a:hover {
  text-decoration: underline;
}
</style>
