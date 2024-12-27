<template>
  <div class="file-list-component">
    <h2>文件列表</h2>
    <ul>
      <li v-for="file in files" :key="file.name" class="file-item">
        <span>{{ file.name }}</span>
        <button @click="selectFile(file.name)">选择</button>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'FileList',
  props: {
    files: {
      type: Array,
      required: true,
    },
  },
  methods: {
    selectFile(fileName) {
      this.$emit('fileSelected', fileName);
    },
  },
};
</script>

<style scoped>
.file-list-component {
  max-width: 600px; /* 设置最大宽度 */
  margin: 0 auto; /* 将组件水平居中 */
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

ul {
  list-style-type: none;
  padding: 0;
  margin: 0; /* 确保列表没有外边距 */
}

.file-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px;
  margin-bottom: 10px;
  background-color: #ffffff;
  border: 1px solid #ddd;
  border-radius: 4px;
  transition: background-color 0.3s ease;
}

.file-item:hover {
  background-color: #f1f1f1;
}

button {
  padding: 5px 10px;
  font-size: 14px;
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
