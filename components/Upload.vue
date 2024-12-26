<template>
    <div>
      <h2>Upload Media</h2>
      <input type="file" @change="onFileChange" />
      <button @click="uploadFile">Upload</button>
    </div>
  </template>
  
  <script>
  import axios from 'axios';
  
  export default {
    data() {
      return {
        selectedFile: null,
      };
    },
    methods: {
      onFileChange(event) {
        this.selectedFile = event.target.files[0];
      },
      async uploadFile() {
        if (!this.selectedFile) {
          alert('Please select a file first.');
          return;
        }
  
        const formData = new FormData();
        formData.append('file', this.selectedFile);
  
        try {
          const response = await axios.post('http://localhost:3000/upload', formData, {
            headers: {
              'Content-Type': 'multipart/form-data',
            },
          });
          alert('File uploaded successfully');
          this.$emit('fileUploaded');
        } catch (error) {
          console.error('Error uploading file:', error);
        }
      },
    },
  };
  </script>
  