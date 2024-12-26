<template>
  <div id="app">
    <h1>Control Client</h1>
    <Upload @fileUploaded="refreshFiles" />
    <ControlPanel />
  </div>
</template>

<script>
import Upload from './components/Upload.vue';
import ControlPanel from './components/ControlPanel.vue';

export default {
  components: {
    Upload,
    ControlPanel,
  },
  methods: {
    refreshFiles() {
      this.$refs.controlPanel.$refs.fileList.fetchFiles();
    },
  },
};
</script>
