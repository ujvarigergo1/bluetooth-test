<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-title>Blank</ion-title>
      </ion-toolbar>
    </ion-header>
    
    <ion-content :fullscreen="true">
      <ion-item>
          <ion-label class="ion-text-wrap">{{message}}</ion-label>
        </ion-item>
        <ion-item>
          <ion-label class="ion-text-wrap">{{data}}</ion-label>
        </ion-item>
      <ion-button @click="getDevices()"> Get devices</ion-button>
      <ion-button @click="write()"> Write</ion-button>
      <ion-button @click="read()"> Read</ion-button>
      <ion-button @click="disconnect()"> Disconnect</ion-button>
      <ion-button @click="$router.push('/serial')"> Serial</ion-button>
      <ion-list>
        <ion-item v-for="device in devices" :key="device" button="true" @click="connect(device.address)">
          <ion-label>{{device.name}} {{device.address}}</ion-label>
        </ion-item>
      </ion-list>
      
    </ion-content>
  </ion-page>
</template>

<script lang="ts">
import { IonContent, IonHeader, IonPage, IonTitle, IonToolbar, IonButton, IonList, IonLabel,IonItem } from '@ionic/vue';
import { defineComponent } from 'vue';
import { BleClient } from '@capacitor-community/bluetooth-le';
import { BluetoothSerial } from '@ionic-native/bluetooth-serial/ngx';

export default defineComponent({
  name: 'Home',
  components: {
    IonContent,
    IonHeader,
    IonPage,
    IonTitle,
    IonToolbar,
    IonButton,
    IonList, IonLabel,IonItem
  },
  methods:{
    disconnect(){
      this.bs.disconnect()
    },
    read(){
      this.bs.readUntil('\n').then(success => {this.message =success})
    },
    write(){
      this.bs.write('hello world')
    },
    connect(mac: any){
      this.bs.connectInsecure(mac).subscribe(success => {this.data =success}, error=> {this.data = error})
      
    },
    async getDevices(){
      this.devices = await this.bs.list()
      console.log(this.devices)
      //this.data = this.bs.subscribe('\n',).then((success: any) => this.data = success)
      //await BleClient.initialize()
    //BleClient.requestDevice().then((resp) =>BleClient.connect(resp.deviceId).then((response) => console.log(BleClient.getConnectedDevices([]))))
    }
    
  },
  data: function() {
      return {
          devices:[],
          bs: new BluetoothSerial,
          message:"",
          data: {}
      }
    },
});
</script>

<style scoped>
#container {
  text-align: center;
  
  position: absolute;
  left: 0;
  right: 0;
  top: 50%;
  transform: translateY(-50%);
}

#container strong {
  font-size: 20px;
  line-height: 26px;
}

#container p {
  font-size: 16px;
  line-height: 22px;
  
  color: #8c8c8c;
  
  margin: 0;
}

#container a {
  text-decoration: none;
}
</style>