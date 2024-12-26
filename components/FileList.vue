<template>
    <div>
      <h2>Available Media Files</h2>
      <ul>
        <li v-for="file in files" :key="file">{{ file }}</li>
      </ul>
    </div>
  </template>
  
  <script>
  import axios from 'axios';
  
  export default {
    data() {
      return {
        files: [],
      };
    },
    created() {
      this.fetchFiles();
    },
    methods: {
      async fetchFiles() {
        try {
          const response = await axios.get('http://localhost:3000/files');
          this.files = response.data;
        } catch (error) {
          console.error('Error fetching files:', error);
        }
      },
    },
  };
  </script>
  