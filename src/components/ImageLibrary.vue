<template>
  <div class="image-library">
    <div class="header">
      <h2>图片库</h2>
      <div>
        <button @click="triggerUpload" class="header-button">
          <i class="fa fa-upload" /> 上传
        </button>
        <button @click="fetchFiles" class="header-button">
          <i class="fa fa-rotate-right" /> 刷新
        </button>
      </div>
      <input type="file" ref="fileInput" accept="image/*" @change="uploadFile" style="display: none" />
    </div>
    <div class="image-list">
      <div v-for="(file, index) in files" :key="index" class="image-item">
        <span>{{ file }}</span>
        <button @click="deleteFile(file)" class="delete-button">
          <i class="fa fa-xmark" />
        </button>
        <button @click="playFile(file)" class="select-button">
          <i class="fa fa-check" />
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
  methods: {
    // 获取图片列表
    fetchFiles() {
      axios
        .get("https://api.xxtsoft.top/fes/images")
        .then((response) => {
          this.files = response.data;
        })
        .catch((error) => {
          console.error("Error fetching files:", error);
        });
    },
    // 触发图片上传
    triggerUpload() {
      this.$refs.fileInput.click();
    },
    // 上传图片
    uploadFile(event) {
      const file = event.target.files[0]
      if (!file) return;

      const formData = new FormData();
      formData.append("file", file, encodeURIComponent(file.name))

      axios
        .post("https://api.xxtsoft.top/fes/upload-image", formData, {
          headers: {
            "Content-Type": "multipart/form-data",
          },
        })
        .then(() => {
          this.fetchFiles(); // 刷新列表
          // alert("图片上传成功！");
        })
        .catch((error) => {
          console.error("Error uploading file:", error);
        });
    },

    // 删除图片
    deleteFile(file) {
      axios
        .get(`https://api.xxtsoft.top/fes/remove-image?file=${file}`)
        .then(() => {
          this.fetchFiles(); // 刷新列表
          // alert("图片删除成功！");
        })
        .catch((error) => {
          console.error("Error deleting file:", error);
        });
    },
    // 发送谢欢指令
    playFile(file) {
      console.log(`Command sent: image show "${file}"`);
      ws.send(JSON.stringify(
        { target: 'presentation', type: 'image', file: file }
      ));
    }
  },
  mounted() {
    this.fetchFiles(); // 初始化加载图片列表
  },
};
</script>

<style scoped>
.image-library {
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

.image-list {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.image-item {
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

.image-item:hover {
  background-color: #f1f1f1;
}

.image-item span {
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

.select-button {
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

.selecct-button:hover {
  background-color: #16a34a;
}
</style>