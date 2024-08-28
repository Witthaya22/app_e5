<template>
  <v-container fluid class="fill-height d-flex flex-column align-center justify-center">
      <v-card-title class="d-flex justify-center">
        <h1 :title="msg">{{ msg }}</h1>
      </v-card-title>

      <v-card-subtitle class="text-center">
        <h2>{{ status }}</h2>
      </v-card-subtitle>

      <v-card-text class="d-flex justify-center">
        <v-btn
          v-if="status === 'MQTT connected'"
          class="ma-2"
          color="primary"
          @click="toggleConnection"
        >
          {{ status }}
          <v-icon end>mdi-checkbox-marked-circle</v-icon>
        </v-btn>
        <v-btn
          v-else
          class="ma-2"
          color="red"
          @click="toggleConnection"
        >
          {{ status }}
          <v-icon end>mdi-cancel</v-icon>
        </v-btn>
      </v-card-text>

      <v-card-text class="text-center">
        <h3>Temperature</h3>
        <v-progress-circular
          :model-value="value"
          :rotate="360"
          :size="100"
          :width="15"
          color="teal"
        >
          <template v-slot:default> {{ value }} °C </template>
        </v-progress-circular>
      </v-card-text>

      <v-card-text class="text-center">
        <h3>Humidity</h3>
        <v-progress-circular
          :model-value="value1"
          :rotate="360"
          :size="100"
          :width="15"
          color="red"
        >
          <template v-slot:default> {{ value1 }} % </template>
        </v-progress-circular>
      </v-card-text>

      <v-card-actions class="d-flex justify-center">
        <v-text-field v-model="id" label="Set Temperature" type="number" hide-details dense></v-text-field>
        <v-btn class="ma-2" color="primary" @click="add">Set</v-btn>
        <v-text-field v-model="humidity" label="Set Humidity" type="number" hide-details dense></v-text-field>
        <v-btn class="ma-2" color="primary" @click="setHumidity">Set</v-btn>
      </v-card-actions>
  </v-container>
</template>

<script>
import mqtt from "mqtt";
export default {
  data() {
    return {
      id: 10,
      msg: "",
      status: "Disconnected",
      value: 10,
      value1: 60,
      humidity: 50,
      client: null,
      isConnected: false,
    };
  },

  created() {
    this.connectMqtt();
  },

  beforeDestroy() {
    this.disconnectMqtt();
  },

  methods: {
    connectMqtt() {
      this.client = mqtt.connect("ws://broker.emqx.io:8083/mqtt");
      this.client.on("connect", this.onMqttConnect);
      this.client.on("message", this.onMqttMessage);
    },

    disconnectMqtt() {
      if (this.client) {
        this.client.end();
        this.status = "Disconnected";
        this.isConnected = false;
        console.log("Disconnected from MQTT");
      }
    },

    toggleConnection() {
      if (this.isConnected) {
        this.disconnectMqtt();
      } else {
        this.connectMqtt();
      }
    },

    add() {
      this.value = this.id++;
      this.client.publish("room1/sw01", String(this.value));
    },

    setHumidity() {
      this.value1 = this.humidity++;
      this.client.publish("room1/humidity", String(this.value1));
      // this.status = `Humidity set to ${this.value1}%`;
    },


    onMqttConnect() {
      this.status = "MQTT connected";
      this.isConnected = true;
      console.log("Connected to MQTT");
    },

    onMqttMessage(topic, message) {
      console.log(`Received message on ${topic}: ${message.toString()}`);
      if (topic === "room1/temperature") {
        this.value = parseInt(message.toString(), 10);
      } else if (topic === "room1/humidity") {
        this.value1 = parseInt(message.toString(), 10);
      }
    },
  },
};
</script>
