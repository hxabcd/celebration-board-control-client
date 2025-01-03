<template>
  <div class="music-library">
    <div class="header">
      <h2>音乐库</h2>
      <div>
        <button @click="triggerUpload" class="header-button">
          <i class="fa fa-upload" /> 上传
        </button>
        <button @click="fetchFiles" class="header-button">
          <i class="fa fa-rotate-right" /> 刷新
        </button>
      </div>
      <input type="file" ref="fileInput" accept="audio/*" @change="uploadFile" style="display: none" />
    </div>
    <div class="music-list">
      <div v-for="(file, index) in files" :key="index" class="music-item">
        <span>{{ file }}</span>
        <button @click="deleteFile(file)" class="delete-button">
          <i class="fa fa-xmark" />
        </button>
        <button @click="playFile(file)" class="play-button">
          <i class="fa fa-play" />
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import ws from './WebSocketService';

export default {
  data() {
    return {
      files: [],
    };
  },
  mounted() {
    this.fetchFiles(); // 初始化加载音乐列表
  },
  methods: {
    // 获取音乐列表
    fetchFiles() {
      axios
      .get("https://api.xxtsoft.top/fes/musics")
      .then((response) => {
        this.files = response.data;
      })
        .catch((error) => {
          console.error("Error fetching files:", error);
        });
    },
    // 触发文件上传
    triggerUpload() {
      this.$refs.fileInput.click();
    },
    // 上传文件
    uploadFile(event) {
      const file = event.target.files[0]
      if (!file) return;

      const formData = new FormData();
      formData.append("file", file, encodeURIComponent(file.name))

      axios
        .post("https://api.xxtsoft.top/fes/upload-music", formData, {
          headers: {
            "Content-Type": "multipart/form-data",
          },
        })
        .then(() => {
          this.fetchFiles(); // 刷新列表
          // alert("文件上传成功！");
        })
        .catch((error) => {
          console.error("Error uploading file:", error);
        });
    },

    // 删除文件
    deleteFile(file) {
      axios
        .get(`https://api.xxtsoft.top/fes/remove-music?file=${file}`)
        .then(() => {
          this.fetchFiles(); // 刷新列表
          // alert("文件删除成功！");
        })
        .catch((error) => {
          console.error("Error deleting file:", error);
        });
    },
    // 发送播放指令
    playFile(file) {
      console.log(`Command sent: music play "${file}"`);
      ws.send(JSON.stringify({
        target: 'presentation', type: 'music', action: 'play', file: file
      }));
    }
  },
};
</script>

<style scoped>
.music-library {
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

.header-button {
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

.header-button:hover {
  /* background-color: #4338ca; */
  background-color: #0056b3;
}

.music-list {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.music-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  /* background-color: #c7d2fe; */
  background-color: #ffffff;
  /* font-size: 14px; */
  border: 1px solid #ddd;
  border-radius: 4px;
  padding: 10px;
  margin: 0 8px;
  border-radius: 6px;
  transition: background-color 0.3s ease, color 0.3s ease;
}

.music-item:hover {
  background-color: #f1f1f1;
}

.music-item span {
  flex-grow: 1;
}

.delete-button {
  background-color: #ef4444;
  color: white;
  border: none;
  height: 24px;
  width: 24px;
  font-size: 12px;
  border-radius: 4px;
  margin-left: 4px;
  margin-right: 8px;
  cursor: pointer;
  transition: background-color 0.3s ease, color 0.3s ease;
}

.delete-button:hover {
  background-color: #dc2626;
}

.play-button {
  background-color: #22c55e;
  color: white;
  border: none;
  height: 24px;
  width: 24px;
  font-size: 12px;
  border-radius: 4px;
  margin-right: 8px;
  cursor: pointer;
  transition: background-color 0.3s ease, color 0.3s ease;
}

.play-button:hover {
  background-color: #16a34a;
}
</style>