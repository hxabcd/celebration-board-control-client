<template>
  <div class="music-control">
    <div class="header">
      <h2>播放控制</h2>
      <button @click="getStatus" class="refreash-button">
        <i class="fa fa-rotate-right" /> 刷新
      </button>
    </div>
    <div>
      <p id="now-playing">未播放</p>
      <div class="control-panel">
        <button class="control-button" @click="toggleMusicPause">
          <i id="toggleButtonIcon" class="fa fa-play" />
        </button>
        <button class="control-button" @click="stopMusic">
          <i class="fa fa-stop" />
        </button>
        <div class="volume-bar">
          <p>音量</p>
          <input id="volume-input" type="range" v-model="volume" @input="setVolume" min="0" max="100" />
        </div>
      </div>
    </div>
    <audio ref="audioPlayer" controls style="display: none"></audio>
  </div>
</template>

<script>
import ws from './WebSocketService';

export default {
  data() {
    return {
      nowPlaying: null,
      paused: null,
      volume: 50,
    };
  },
  mounted() {
    ws.onMessage(this.handleMessage);
    ws.onOpen(this.getStatus);
  },
  methods: {
    handleMessage(message) {
      const data = JSON.parse(message);
      if (data.target === 'control') {
        switch (data.key) {
          case 'nowPaying':
            console.log(`nowPlaying: ${data.value}`)
            this.nowPlaying = data.value;
            document.getElementById('now-playing').innerText = data.value ? data.value : '未播放';
            break;
          case 'volume':
            console.log(`Volume: ${data.value}`)
            this.volume = data.value * 100;
            document.getElementById('volume-input').value = data.value * 100;
            break;
          case 'paused':
            console.log(`Paused: ${data.value}`)
            this.paused = data.value;
            document.getElementById('toggleButtonIcon').className = data.value ? 'fa fa-play' : 'fa fa-pause';
            break;
          default:
            console.error('Unknown message key:', data.key);
        }
      }
    },
    getStatus() {
      console.log('获取播放状态：');
      ws.send(JSON.stringify({ target: 'presentation', type: 'get', key: 'nowPlaying' }))
      ws.send(JSON.stringify({ target: 'presentation', type: 'get', key: 'volume' }))
      ws.send(JSON.stringify({ target: 'presentation', type: 'get', key: 'paused' }))
    },
    toggleMusicPause() {
      if (this.paused)  {
        ws.send(JSON.stringify({ target: 'presentation', type: 'music', action: 'play' }));
      } else {
        ws.send(JSON.stringify({ target: 'presentation', type: 'music', action: 'pause' }));
      }
    },
    stopMusic() {
      ws.send(JSON.stringify({ target: 'presentation', type: 'music', action: 'stop' }));
    },
    setVolume() {
      ws.send(JSON.stringify({ target: 'presentation', type: 'music', action: 'volume', value: this.volume / 100 }));
    },
  },
  unmounted() {
    ws.offMessage(this.handleMessage);
    ws.offOpen(this.getStatus);
  }
};
</script>

<style scoped>
.music-control {
  max-width: 600px;
  padding: 20px 10px;
  margin: 20px 0;
  /* background-color: #e0e7ff; */
  background-color: #f8f9fa;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 10px;
}

h2 {
  margin: 10px;
  font-size: 24px;
  color: #333;
}

.refreash-button {
  /* background-color: #4f46e5; */
  background-color: #007bff;
  color: white;
  border: none;
  margin: 0 4px;
  padding: 10px 16px;
  font-size: 16px;
  border-radius: 6px;
  cursor: pointer;
  transition: background-color 0.3s ease, color 0.3s ease;
}

.refreash-button:hover {
  /* background-color: #4338ca; */
  background-color: #0056b3;
}

.control-panel {
  display: flex;
  padding-left: 18px;
}

#now-playing {
  margin: 16px;
  /* padding-left: 30px; */
  text-align: center;
  font-weight: bold;
  font-size: 24px;
}

.control-button {
  height: 40px;
  width: 40px;
  background-color: #007bff;
  color: white;
  border: none;
  padding: 10px 10px;
  margin: 4px;
  font-size: 16px;
  border-radius: 30px;
  cursor: pointer;
  transition: background-color 0.3s ease, color 0.3s ease;
}

.control-button:hover {
  /* background-color: #4338ca; */
  background-color: #0056b3;
}

.volume-bar {
  display: flex;
  margin-left: 16px;
}

#volume-input {
  width: 108px;
}
</style>