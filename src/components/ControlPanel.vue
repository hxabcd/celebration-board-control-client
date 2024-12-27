<template>
  <div class="control-panel">
    <h2>控制面板</h2>
    <div class="file-list">
      <FileList ref="fileList" />
    </div>
    <div class="buttons">
      <button @click="sendImageCommand('background1.jpg')">设置背景</button>
      <button @click="sendAudioCommand('music1.mp3')">播放音乐</button>
    </div>
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
      console.log('WebSocket 连接已打开');
    };
    this.ws.onmessage = (event) => {
      console.log('接收到:', event.data);
    };
    this.ws.onclose = () => {
      console.log('WebSocket 连接已关闭');
    };
  },
  methods: {
    sendImageCommand(filename) {
      if (this.ws.readyState === WebSocket.OPEN) {
        this.ws.send(`image:${filename}`);
      } else {
        console.error('WebSocket 未打开');
      }
    },
    sendAudioCommand(filename) {
      if (this.ws.readyState === WebSocket.OPEN) {
        this.ws.send(`audio:${filename}`);
      } else {
        console.error('WebSocket 未打开');
      }
    },
  },
};
</script>

<style scoped>
.control-panel {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20px;
  /* background-color: #f8f9fa; */
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

h2 {
  margin-bottom: 20px;
  font-size: 24px;
  color: #343a40;
}

.file-list {
  width: 100%;
  margin-bottom: 20px;
}

.buttons {
  display: flex;
  gap: 10px;
}

button {
  padding: 10px 20px;
  font-size: 16px;
  color: #fff;
  background-color: #007bff;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

button:hover {
  background-color: #0056b3;
}
</style>
