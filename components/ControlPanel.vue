<template>
    <div>
      <h2>Control Panel</h2>
      <FileList ref="fileList" />
      <button @click="sendCommand('next')">Next</button>
      <button @click="sendCommand('previous')">Previous</button>
    </div>
  </template>
  
  <script>
  import FileList from './FileList.vue';
  
  export default {
    components: {
      FileList,
    },
    data() {
      return {
        ws: null,
      };
    },
    created() {
      this.ws = new WebSocket('ws://localhost:3000');
      this.ws.onopen = () => {
        console.log('WebSocket connection opened');
      };
      this.ws.onmessage = (event) => {
        console.log('Received:', event.data);
      };
      this.ws.onclose = () => {
        console.log('WebSocket connection closed');
      };
    },
    methods: {
      sendCommand(command) {
        if (this.ws.readyState === WebSocket.OPEN) {
          this.ws.send(command);
        } else {
          console.error('WebSocket is not open');
        }
      },
    },
  };
  </script>
  