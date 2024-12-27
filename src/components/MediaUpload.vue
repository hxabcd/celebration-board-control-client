<template>
  <div class="upload-component">
    <h2>上传媒体文件</h2>
    <input type="file" @change="onFileChange" />
    <button @click="uploadFile" :disabled="!selectedFile || uploading">
      {{ uploading ? '上传中...' : '上传文件' }}
    </button>
    <div v-if="uploading" class="progress-bar">
      <div class="progress" :style="{ width: progress + '%' }"></div>
    </div>
    <p v-if="uploadMessage" :class="{ 'success-message': uploadSuccess, 'error-message': !uploadSuccess }">
      {{ uploadMessage }}
    </p>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'MediaUpload',
  data() {
    return {
      selectedFile: null,
      uploading: false,
      progress: 0,
      uploadMessage: '',
      uploadSuccess: false,
    };
  },
  methods: {
    onFileChange(event) {
      this.selectedFile = event.target.files[0];
      this.uploadMessage = ''; // Clear previous messages
    },
    async uploadFile() {
      if (!this.selectedFile) {
        alert('请先选择一个文件。');
        return;
      }

      this.uploading = true;
      this.progress = 0;

      const formData = new FormData();
      formData.append('file', this.selectedFile);

      try {
        const response = await axios.post('http://localhost:3000/upload', formData, {
          headers: {
            'Content-Type': 'multipart/form-data',
          },
          onUploadProgress: (progressEvent) => {
            this.progress = Math.round((progressEvent.loaded * 100) / progressEvent.total);
          },
        });
        this.uploadMessage = '文件上传成功';
        this.uploadSuccess = true;
        this.$emit('fileUploaded', response.data);
      } catch (error) {
        this.uploadMessage = '上传文件时出错';
        this.uploadSuccess = false;
        console.error('上传文件时出错:', error);
      } finally {
        this.uploading = false;
      }
    },
  },
};
</script>

<style scoped>
.upload-component {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  background-color: #f8f9fa;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

h2 {
  margin-bottom: 20px;
  font-size: 24px;
  color: #343a40;
}

input[type="file"] {
  margin-bottom: 15px;
}

button {
  padding: 10px 20px;
  font-size: 16px;
  color: #fff;
  background-color: #28a745;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}

button:hover:enabled {
  background-color: #218838;
}

.progress-bar {
  width: 100%;
  background-color: #e9ecef;
  border-radius: 4px;
  overflow: hidden;
  margin-top: 10px;
}

.progress {
  height: 20px;
  background-color: #28a745;
  transition: width 0.3s ease;
}

.success-message {
  color: #28a745;
  margin-top: 10px;
}

.error-message {
  color: #dc3545;
  margin-top: 10px;
}
</style>
