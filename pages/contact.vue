<template>
    <v-container fluid class="fill-height ">
        <v-card-title class="d-flex justify-center">
          <h1>ติดต่อเรา</h1>
        </v-card-title>
  
        <v-divider class="my-4"></v-divider>
  
        <v-card-text>
          <v-form>
            <v-text-field
              v-model="name"
              label="ชื่อ"
              outlined
              required
            ></v-text-field>
  
            <v-text-field
              v-model="email"
              label="อีเมล"
              type="email"
              outlined
              required
            ></v-text-field>
  
            <v-text-field
              v-model="subject"
              label="เรื่อง"
              outlined
            ></v-text-field>
  
            <v-textarea
              v-model="message"
              label="ข้อความ"
              rows="5"
              outlined
              required
            ></v-textarea>
  
            <v-btn
              class="mt-4"
              color="primary"
              @click="sendMessage"
              :disabled="!formValid"
            >
              ส่งข้อความ
            </v-btn>
          </v-form>
        </v-card-text>
  
        <v-divider class="my-4"></v-divider>
  
        <v-card-text class="text-center">
          <span>
            <v-icon large color="primary">mdi-phone</v-icon>
            <p class="mt-2">+66 123 456 7890</p>
          </span>
          <span>
          <v-icon large color="primary">mdi-email</v-icon>
          <p class="mt-2">66309010031@ccollege.ac.th</p>
        </span>
        <span>
          <v-icon large color="primary">mdi-map-marker</v-icon>
          <p class="mt-2">260 ตำบล ในเมือง อำเภอเมืองชัยภูมิ ชัยภูมิ 36000</p>
        </span>
        </v-card-text>
      </v-container>
  </template>
  
  <script>
  export default {
    data() {
      return {
        name: "",
        email: "",
        subject: "",
        message: "",
      };
    },
  
    computed: {
      formValid() {
        return this.name && this.email && this.message;
      },
    },
  
    methods: {
      sendMessage() {
        if (this.formValid) {
          // Here you would typically send the data to a server
          alert(`Message sent by ${this.name} (${this.email}): ${this.message}`);
          // Reset the form
          this.name = "";
          this.email = "";
          this.subject = "";
          this.message = "";
        } else {
          alert("Please fill out all required fields.");
        }
      },
    },
  };
  </script>
  
  <style scoped>
  .v-card-title h1 {
    color: #34495e;
    font-weight: bold;
  }
  
  .v-card-text p {
    margin: 0;
  }
  
  .v-card-text .v-icon {
    margin-bottom: 4px;
  }
  
  .v-card-text .v-icon ~ p {
    margin-bottom: 16px;
  }
  
  </style>
  