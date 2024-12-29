<template>
  <div id="app">
    <h1>晚会看板控制端</h1>
    <p id="connecting-status">未连接</p>
    <div class="Panel">
      <div class="MusicPanel">
        <MusicControl />
        <MusicLibrary @fileUploaded="refreshFiles" />
      </div>
      <div class="ImagePanel">
        <ImageLibrary @fileUploaded="refreshFiles" />
      </div>
    </div>
  </div>
</template>

<script>
import MusicLibrary from './components/MusicLibrary.vue';
import MusicControl from './components/MusicControl.vue';
import ImageLibrary from './components/ImageLibrary.vue';
import ws from './components/WebSocketService';

export default {
  components: {
    MusicLibrary,
    MusicControl,
    ImageLibrary,
  },
  data() {
    return {
      connectionStatus: '连接断开',
    };
  },
  watch: {
    connectionStatus(newStatus) {
      document.title = `晚会看板控制端 | ${newStatus}`;
      document.getElementById('connecting-status').innerText = this.connectionStatus;
    },
  },
  created() {
    document.title = `晚会看板控制端 | ${this.connectionStatus}`;
    ws.onOpen( () => {
      this.connectionStatus = '已连接';
    });
    ws.onClose( () => {
      this.connectionStatus = '连接断开';
    });
  },
  methods: {
    refreshFiles() {
      this.$refs.controlPanel.$refs.fileList.fetchFiles();
    },
  },
  beforeUnmount() {
    ws.offOpen(this.handleOpen);
    ws.offClose(this.handleClose);
  }
};
</script>

<style>
@media screen and (min-width: 480px) {
  .Panel {
    display: flex;
    justify-content: space-around;
  }

  .MusicPanel {
    width: 50%;
    margin: auto;
  }

  .ImagePanel {
    flex: 1;
    width: 50%;
  }
}

h1 {
  text-align: center;
}

#connecting-status {
  text-align: center;
}

.MusicPanel {
  padding: 10px 10px;
}

.ImagePanel {
  padding: 10px 10px;
}
</style>