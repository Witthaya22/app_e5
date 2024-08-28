<template>
  <v-app>
    <!-- Hero Image -->
    <v-img
      src="../assets/images/farm3.jpg"
      
      class="hero-image"
    >
      <v-container fill-height style="">
        <v-row align="center" justify="center" class="fill-height">
          <v-col >
            <v-card class="text-center hero-card" elevation="8">
              <v-card-title class="headline white--text">ยินดีต้อนรับ สู่ เว็บไซต์ชาวนาประเทศไทย</v-card-title>
              <v-card-subtitle class="white-text">"เว็บชาวนาประเทศไทย" เป็นแพลตฟอร์มที่ออกแบบมาเพื่อสนับสนุนชาวนาในประเทศไทย ด้วยการให้ข้อมูลที่เป็นประโยชน์เกี่ยวกับการเกษตร เทคนิคใหม่ ๆ และการจัดการฟาร์ม เว็บไซต์ของเรายังเป็นพื้นที่สำหรับการแลกเปลี่ยนประสบการณ์และคำแนะนำระหว่างชุมชนเกษตรกร พร้อมทั้งการอัปเดตข่าวสารล่าสุดเกี่ยวกับเกษตรกรรม เพื่อให้ชาวนาสามารถเพิ่มผลผลิตและการพัฒนาอย่างยั่งยืนได้อย่างมีประสิทธิภาพ"</v-card-subtitle>
            </v-card>
          </v-col>
        </v-row>
      </v-container>
    </v-img>

    <!-- Main Content -->
    <!-- <v-main>
      <v-container class="pa-5">
        <v-card class="bg-blur">
          <v-card-title>Content Title</v-card-title>
          <v-card-text>
            This is where you put the content of your page.
          </v-card-text>
        </v-card>
      </v-container>
    </v-main> -->
  </v-app>
</template>

<script>
export default {
  name: 'Home',
};
</script>

<style scoped>
.hero-image {
  position: relative;
  background-position: center;
  background-size: cover;
  background-repeat: no-repeat;
}

.hero-card {
  position: relative;
  padding: 16px;
  background: rgba(255, 255, 255, 0.5); /* Semi-transparent background for the text */
}

.bg-blur {
  backdrop-filter: blur(10px); /* Apply blur effect */
  -webkit-backdrop-filter: blur(10px); /* For Safari support */
  background: rgba(255, 255, 255, 0.6); /* Semi-transparent white background for contrast */
}
</style>
