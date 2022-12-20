<template>
    <ion-page>
        <ion-content :fullscreen="true">
        <ion-item>
            <ion-label class="ion-text-wrap">{{message}}</ion-label>
            </ion-item>
            {{data}}
        <ion-button @click="write()"> Write</ion-button>
        <ion-button @click="read()"> Read</ion-button>
        <ion-button @click="disconnect()"> Disconnect</ion-button>
        <ion-button @click="$router.push('/')">Bluetooth</ion-button>
        <ion-button @click="connectBrowserSerial()">Browser serial connect</ion-button>
        
        
        </ion-content>
    </ion-page>
</template>

<script lang="js">
import { defineComponent } from 'vue'
import { Serial } from '@ionic-native/serial/ngx';
import { isPlatform } from '@ionic/vue';

export default defineComponent({
    name:'Serial',
    data: function() {
      return {
          devices:[],
          serial: new Serial,
          message:"",
          data: [0],
          isCordova: isPlatform('cordova'),
          //SerialPort: require('serialport')
      }
    },
    methods:{
    processData(data){
        
        data.forEach((element) => {
            if(element !== 0)
                this.data.push(element/100)
        });
    },
    disconnect(){
      this.serial.close()
    },
    
    write(){
      this.serial.write('d')
    },
    
    connect(){
        this.serial.requestPermission().then(() => {
        this.serial.open({
            baudRate: 115200,
            dataBits: 8,
            stopBits: 1,
            parity: 0,
            dtr: false,
            rts: false,
            sleepOnPause: false
        }).then((response) => {
            this.message = response
            this.serial.registerReadCallback().subscribe(success => {this.processData(new Uint16Array(success))}, error=> {this.data = error})
        });
        }).catch((error) => this.message = error);
    },

    connectBrowserSerial(){
    
        navigator.serial.getPorts().then((ports) => {
            console.log(ports)
          // Initialize the list of available ports with `ports` on page load.
        });
        navigator.serial.requestPort().then((port) => {
            console.log(port)
    // Connect to `port` or add it to the list of available ports.
  }).catch((e) => {
      console.log(e)
    // The user didn't select a port.
  });
        //const Readline = this.SerialPort.parsers.Readline
        //const port = new this.SerialPort('dev')
        //const parser = new Readline()
        //port.pipe(parser)
        //parser.on('data', console.log)
        //port.write('ROBOT PLEASE RESPOND\n')
        // ROBOT ONLINE
    }
  },
    mounted(){
        if(this.isCordova){
            this.connect()
        } 
    }
})
</script>
