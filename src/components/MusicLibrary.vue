<template>
  <div class="music-library">
    <div class="header">
      <h2>音乐库</h2>
      <button @click="triggerUpload" class="upload-button">
        <i class="fa fa-upload" /> 上传
      </button>
      <input
        type="file"
        ref="fileInput"
        accept="audio/*"
        @change="uploadFile"
        style="display: none"
      />
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
    <audio ref="audioPlayer" controls style="display: none"></audio>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      files: [],
    };
  },
  methods: {
    // 获取音乐列表
    fetchFiles() {
      axios
        .get("http://localhost:3000/files")
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
      const file = event.target.files[0];
      if (!file) return;

      const formData = new FormData();
      formData.append("file", file);

      axios
        .post("http://localhost:3000/upload", formData, {
          headers: {
            "Content-Type": "multipart/form-data",
          },
        })
        .then(() => {
          this.fetchFiles(); // 刷新列表
          alert("文件上传成功！");
        })
        .catch((error) => {
          console.error("Error uploading file:", error);
        });
    },
    // 删除文件
    deleteFile(file) {
      axios
        .delete(`http://localhost:3000/uploads/${file}`)
        .then(() => {
          this.fetchFiles(); // 刷新列表
          alert("文件删除成功！");
        })
        .catch((error) => {
          console.error("Error deleting file:", error);
        });
    },
    // 播放文件
    playFile(file) {
      const audioPlayer = this.$refs.audioPlayer;
      audioPlayer.src = `http://localhost:3000/uploads/${file}`;
      audioPlayer.play();
    },
  },
  mounted() {
    this.fetchFiles(); // 初始化加载音乐列表
  },
};
</script>

<style scoped>
.music-library {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  background-color: #e0e7ff;
  /* background-color: #f8f9fa; */
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
  margin: 0;
  font-size: 24px;
  color: #333;
}

.upload-button {
  background-color: #4f46e5;
  color: white;
  border: none;
  padding: 10px 20px;
  font-size: 16px;
  border-radius: 4px;
  cursor: pointer;
}

.upload-button:hover {
  background-color: #4338ca;
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
  background-color: #c7d2fe;
  padding: 10px;
  border-radius: 4px;
}

.music-item span {
  flex-grow: 1;
}

.delete-button {
  background-color: #ef4444;
  color: white;
  border: none;
  padding: 5px 5px;
  border-radius: 4px;
  margin-right: 5px;
  cursor: pointer;
}

.delete-button:hover {
  background-color: #dc2626;
}

.play-button {
  background-color: #22c55e;
  color: white;
  border: none;
  padding: 5px 5px;
  border-radius: 4px;
  cursor: pointer;
}

.play-button:hover {
  background-color: #16a34a;
}
</style>