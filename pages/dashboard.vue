<script setup>
import { ref, computed } from "vue";
import mqtt from "mqtt";
import axios from "axios";

const temp = ref(0);
const humi = ref(0);
const state = ref(1);
const msg = ref("");
const status = ref("ไม่ได้ทำการเชื่อมต่อ");
const isConnected = ref(false);
const client = ref(null);
const isFanOn = ref(false);
const currentSilkwormStage = ref(1);// ใช้ state นี้สำหรับเก็บค่า stage ปัจจุบัน

// Define optimal temperature and humidity ranges for each silkworm stage
const silkwormStages = {
  1: { tempMin: 27, tempMax: 28, humiMin: 85, humiMax: 90 },
  2: { tempMin: 26, tempMax: 27, humiMin: 85, humiMax: 90 },
  3: { tempMin: 25, tempMax: 26, humiMin: 80, humiMax: 85 },
  4: { tempMin: 24, tempMax: 25, humiMin: 70, humiMax: 75 },
  5: { tempMin: 24, tempMax: 25, humiMin: 65, humiMax: 70 }
};



// MQTT connection
const connect = () => {
  client.value = mqtt.connect("ws://broker.emqx.io:8083/mqtt");
  client.value.on("connect", onMqttConnect);
  client.value.on("message", onMqttMessage);
  client.value.on("reconnect", handleOnReConnect);
  client.value.on("close", onMqttClose);
};

const disconnect = () => {
  if (client.value) {
    client.value.end();
    resetData();
  }
};


const resetData = () => {
  isConnected.value = false;
  status.value = "ไม่ได้ทำการเชื่อมต่อ";
  msg.value = "";
  temp.value = 25.4;
  humi.value = 79.7;
  state.value = 1;
  isFanOn.value = false;
};

// MQTT event handlers

let openLight 
const onMqttConnect = () => {
  isConnected.value = true;
  status.value = "กำลังเชื่อมต่อ...";
  client.value.subscribe("status", (err) => err && console.error(err));
  client.value.subscribe("e52567/temp", (err) => err && console.error(err));
};


const onMqttMessage = (topic, message) => {
  try {
    const jsonData = JSON.parse(message.toString());
    if (topic === "status") {
      msg.value = JSON.stringify(jsonData);
    }
    if (topic === "e52567/temp") {
      temp.value = jsonData.temp ? parseFloat(jsonData.temp) : temp.value;
      humi.value = jsonData.humi ? parseFloat(jsonData.humi) : humi.value;
      state.value = jsonData.state ? parseInt(jsonData.state) : state.value;
      currentSilkwormStage.value = state.value; // อัปเดต currentSilkwormStage ตามค่า state ที่ได้รับ
      checkAndNotify();
    }
  } catch (error) {
    console.error("Error parsing JSON:", error);
  }
};

const handleOnReConnect = () => {
  console.log("Reconnected to MQTT broker");
};

const onMqttClose = () => {
  console.log("MQTT connection closed");
  resetData();
};

// Monitoring temperature and humidity based on silkworm stage
const isTemperatureNormal = computed(() => {
  const stage = silkwormStages[currentSilkwormStage.value];
  return temp.value >= stage.tempMin && temp.value <= stage.tempMax;
});

const isHumidityNormal = computed(() => {
  const stage = silkwormStages[currentSilkwormStage.value];
  return humi.value >= stage.humiMin && humi.value <= stage.humiMax;
});

// Check and send notification if values are out of the optimal range
const checkAndNotify = async () => {
  if (!isTemperatureNormal.value || !isHumidityNormal.value) {
    await sendLineNotify();
  }
};

// LINE notification
const sendLineNotify = async () => {
  try {
    const message = generateMessage();
    const params = new URLSearchParams({
      message,
      stickerPackageId: "1",
      stickerId: "13",
    });

    const response = await axios.post(
      "http://localhost:3001/send-line-notify",
      params,
      {
        headers: {
          "Content-Type": "application/x-www-form-urlencoded",
          Authorization: `Bearer Eo81uqgNj3DNz8PUKYjpkd59i7gFZWncPK2Cb9irg2x`,
        },
      }
    );

    if (response.status === 200) {
      console.log("Message and sticker sent successfully");
    } else {
      console.error("Failed to send message and sticker", response);
    }
  } catch (error) {
    console.error("Error sending message and sticker", error);
  }
};

// Generate notification message based on current values
const generateMessage = () => {
  let message = "";
  const stage = silkwormStages[currentSilkwormStage.value];

  if (temp.value < stage.tempMin) {
    message += `อุณหภูมิต่ำกว่าปกติ: ${temp.value.toFixed(2)}°C (ปกติ: ${stage.tempMin}-${stage.tempMax}°C)\n`;
  } else if (temp.value > stage.tempMax) {
    message += `อุณหภูมิสูงกว่าปกติ: ${temp.value.toFixed(2)}°C (ปกติ: ${stage.tempMin}-${stage.tempMax}°C)\n`;
  }

  if (humi.value < stage.humiMin) {
    message += `ความชื้นต่ำกว่าปกติ: ${humi.value.toFixed(2)}% (ปกติ: ${stage.humiMin}-${stage.humiMax}%)\n`;
  } else if (humi.value > stage.humiMax) {
    message += `ความชื้นสูงกว่าปกติ: ${humi.value.toFixed(2)}% (ปกติ: ${stage.humiMin}-${stage.humiMax}%)\n`;
  }

  return message;
};

// Display status messages
const messageTemp = computed(() => {
  const stage = silkwormStages[currentSilkwormStage.value];
  if (temp.value < stage.tempMin) {
    return `อุณหภูมิต่ำกว่าปกติ: ${temp.value.toFixed(2)}°C (ปกติ: ${stage.tempMin}-${stage.tempMax}°C)`;
  } else if (temp.value > stage.tempMax) {
    return `อุณหภูมิสูงกว่าปกติ: ${temp.value.toFixed(2)}°C (ปกติ: ${stage.tempMin}-${stage.tempMax}°C)`;
  } else {
    return `อุณหภูมิ ปกติ: ${temp.value.toFixed(2)}°C`;
  }
});

const messageHumi = computed(() => {
  const stage = silkwormStages[currentSilkwormStage.value];
  if (humi.value < stage.humiMin) {
    return `ความชื้นต่ำกว่าปกติ: ${humi.value.toFixed(2)}% (ปกติ: ${stage.humiMin}-${stage.humiMax}%)`;
  } else if (humi.value > stage.humiMax) {
    return `ความชื้นสูงกว่าปกติ: ${humi.value.toFixed(2)}% (ปกติ: ${stage.humiMin}-${stage.humiMax}%)`;
  } else {
    return `ความชื้น ปกติ: ${humi.value.toFixed(2)}%`;
  }
});

// Color change logic
const colorTemp = computed(() => {
  return isTemperatureNormal.value ? "green" : "red";
});

const colorHumi = computed(() => {
  return isHumidityNormal.value ? "green" : "red";
});



// Fan activation logic based on conditions
const activateFan = () => {
  isFanOn.value = true;
  const adjustInterval = setInterval(() => {
    const stage = silkwormStages[currentSilkwormStage.value];
    if (temp.value < stage.tempMax) {
      temp.value += 0.1;
    } else if (temp.value > stage.tempMax) {
      temp.value -= 0.1;
    }

    if (humi.value < stage.humiMax) {
      humi.value += 0.5;
    } else if (humi.value > stage.humiMax) {
      humi.value -= 0.5;
    }

    if (isTemperatureNormal.value && isHumidityNormal.value) {
      clearInterval(adjustInterval);
      isFanOn.value = false;
    }
  }, 500);
};

const addState = () => {
  state.value += 1;
  currentSilkwormStage.value = state.value;
}

const downState = () => {
  state.value -= 1;
  currentSilkwormStage.value = state.value;
}


const colorL1 = computed(() => {
  if(isConnected.value){
    if (isTemperatureNormal.value) {
      return "white";
    } else {
      return "red";
    }
  }
})

const colorL2 = computed(() => {
  if(isConnected.value){
    if (isHumidityNormal.value) {
      return "white";
    } else {
      return "red";
    }
  }
})
</script>




<template>
  <div style="height: 100vh">
    <v-container fluid>
      <!-- Left Side Buttons -->
      <v-row no-gutters>
        <v-col cols="12" md="3" style="">
          <div class="left-panel" style="padding-top: 20vh;">
            <h1 class="title">อุณหภูมิและความชื้นฟาร์ม</h1>
            <h2 :class="isConnected ? 'connected-text' : 'disconnected-text'">{{ status }}</h2>
            <h2 style="font-size: 1.5em;">ช่วงที่วัย: {{ currentSilkwormStage }}</h2>
            
            <v-btn
              class="mb-3"
              color="green"
              @click="connect"
              v-if="!isConnected"
              block
            >
              เชื่อมต่อ
              <v-icon icon="mdi-link" end></v-icon>
            </v-btn>

            <v-btn
              class="mb-3"
              color="red"
              @click="disconnect"
              v-if="isConnected"
              block
            >
              ยกเลิกเชื่อมต่อ
              <v-icon icon="mdi-link-off" end></v-icon>
            </v-btn>



            <!-- <v-btn
              style="margin-bottom: 10px;"
              color="brown"
              @click="addState"
              block
            >
              เพิ่มช่วงวัย
              <v-icon icon="mdi-arrow-up-bold-outline" end></v-icon>
            </v-btn>

            <v-btn
              style="margin-bottom: 10px;"
              color="brown"
              @click="downState"
              block
            >
              ลดช่วงวัย
              <v-icon icon="mdi-arrow-down-bold-outline" end></v-icon>
            </v-btn> -->
            

            
            <div 
              style="display: flex; justify-content: space-between; align-items: center; font-size: 1.2em;"
            >
              <span style="font-weight: bold">ไฟสถานะการทำงาน</span>
              <span 
                :style="{
                  display: 'inline-block',
                  width: '30px',
                  height: '30px',
                  backgroundColor: isConnected ? 'green' : 'white',
                  borderRadius: '50%',
                  border: '2px solid ',
                  transition: 'background-color 0.3s ease'
                }"
              ></span>
            </div>


            <div 
              style="display: flex; justify-content: space-between; align-items: center; font-size: 1.2em;margin-top: 10px;"
            >
              <span style="font-weight: bold">มอเตอร์ระบายความชื้น</span>
              <span 
                :style="{
                  display: 'inline-block',
                  width: '30px',
                  height: '30px',
                  backgroundColor: colorL2,
                  borderRadius: '50%',
                  border: '2px solid ' ,
                  transition: 'background-color 0.3s ease'
                }"
              ></span>
            </div>

            
            <div 
              style="display: flex; justify-content: space-between; align-items: center; font-size: 1.2em;margin-top: 10px;"
            >
              <span style="font-weight: bold">HEATER</span>
              <span 
                :style="{
                  display: 'inline-block',
                  width: '30px',
                  height: '30px',
                  backgroundColor: colorL1,
                  borderRadius: '50%',
                  border: '2px solid ' ,
                  transition: 'background-color 0.3s ease'
                }"
              ></span>
            </div>



           

            <!-- <v-btn
              style="margin-bottom: 10px;"
              color="blue"
              @click="activateFan"
              :disabled="!isConnected || isFanOn"
              block
            >
              เปิดพัดลม
              <v-icon icon="mdi-fan" end></v-icon>
            </v-btn>

            <v-btn
              style="margin-bottom: 10px;color: orange;background-color: orange; color: aliceblue;"
              @click="activateFan"
              :disabled="!isConnected || isFanOn"
              block
            >
              หลอดไฟ
              <v-icon icon="mdi-lightbulb" end></v-icon>
            </v-btn> -->
          </div>
        </v-col>

        <!-- Main Content with Graphs -->
        <v-col cols="12" md="9">
          <!-- Real-time data graphs -->
          <v-row justify="center" class="text-center mt-5">
            <v-col cols="12" md="6">
              <h1>อุณหภูมิ (Real-time)</h1>
              <v-progress-circular
                :model-value="temp"
                :rotate="360"
                :size="180"
                :width="15"
                :color="colorTemp"
                style="font-size: 2.2em"
              >
                <template #default>{{ temp.toFixed(2) }}°C</template>
              </v-progress-circular>
              <h2 class="status-text">{{ messageTemp }}</h2>
            </v-col>
            <v-col cols="12" md="6">
              <h1>ความชื้น (Real-time)</h1>
              <v-progress-circular
                :model-value="humi"
                :rotate="360"
                :size="180"
                :width="15"
                :color="colorHumi"
                style="font-size: 2.2em"
              >
                <template #default>{{ humi.toFixed(2) }}%</template>
              </v-progress-circular>
              <h2 class="status-text">{{ messageHumi }}</h2>
            </v-col>
          </v-row>

          <hr style="margin-top: 2em;max-width: 900px; margin-left: auto; margin-right: auto">

          <v-row justify="center" class="text-center mt-8">
            <v-col cols="12" md="6">
              <h1>อุณหภูมิที่ดีในช่วงที่ {{ currentSilkwormStage }}</h1>
              <v-progress-circular
                :model-value="(silkwormStages[currentSilkwormStage].tempMin + silkwormStages[currentSilkwormStage].tempMax) / 2"
                :rotate="360"
                :size="180"
                :width="15"
                color="green"
                style="font-size: 2.2em"
              >
                <template #default>{{ silkwormStages[currentSilkwormStage].tempMin }} - {{ silkwormStages[currentSilkwormStage].tempMax }}°C</template>
              </v-progress-circular>
            </v-col>
            <v-col cols="12" md="6">
              <h1>ความชื้นที่ดีในช่วงที่ {{ currentSilkwormStage }}</h1>
              <v-progress-circular
                :model-value="(silkwormStages[currentSilkwormStage].humiMin + silkwormStages[currentSilkwormStage].humiMax) / 2"
                :rotate="360"
                :size="180"
                :width="15"
                color="green"
                style="font-size: 2.2em"
              >
                <template #default>{{ silkwormStages[currentSilkwormStage].humiMin }} - {{ silkwormStages[currentSilkwormStage].humiMax }}%</template>
              </v-progress-circular>
            </v-col>
          </v-row>
        </v-col>
      </v-row>
    </v-container>
  </div>
</template>


<style scoped>
.left-panel {
  position: fixed;
  /* padding-left: 2rem; */
}

.status-text {
  font-weight: bold;
}
</style>
