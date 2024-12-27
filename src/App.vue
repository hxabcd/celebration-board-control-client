<template>
  <div id="app">
    <h1>晚会看板</h1>
    <MediaUpload @fileUploaded="refreshFiles" />
    <ControlPanel />
  </div>
</template>

<script>
import MusicLibrary from './components/MusicLibrary.vue';
import ControlPanel from './components/ControlPanel.vue';

export default {
  components: {
    MusicLibrary,
    ControlPanel,
  },
  methods: {
    refreshFiles() {
      this.$refs.controlPanel.$refs.fileList.fetchFiles();
    },
  },
};
</script>
