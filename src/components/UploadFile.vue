<template>
    <div class="file-upload">
        <ElButton type="primary"><input type="file" @change="handleFileChange" ref="fileInput" /></ElButton>
          <button @click="detectPermissionFields">Check/</button>
        </div>
    <div class="split-screen">
      <div class="left-column">
        <div v-if="file1">
          <h2>File Raw</h2>
          <iframe :src="fileUrl1" width="100%" height="500px" frameborder="0"></iframe>
        </div>
      </div>
      <div class="right-column">
        <div v-if="file2">
          <h2>File Result</h2>
          <iframe :src="fileUrl2" width="100%" height="500px" frameborder="0"></iframe>
        </div>
      </div>
    </div>

  </template>
  
  <script>
import { ElButton } from 'element-plus';
 
  export default {
    name: 'UploadFile',
    components:{
      ElButton
    },
    data() {
      return {
        file1: null,
        fileUrl1: '',
        file2: null,
        fileUrl2: ''
      };
    },
    methods: {
      handleFileChange(event) {
          this.file1 = event.target.files[0];
          this.fileUrl1 = URL.createObjectURL(this.file1);
      },
      detectPermissionFields() {
        this.file2 = this.file1;
        this.fileUrl2 = URL.createObjectURL(this.file1);
      }
    }
  };
  </script>
  
  <style scoped>
  .split-screen {
    display: flex;
    flex-direction: row;
  }
  
  .left-column {
    flex: 1;
    background-color: #f0f0f0;
  }
  
  .right-column {
    flex: 1;
    background-color: #e0e0e0;
  }
  
  .file-upload {
    margin: 20px;
  }
  
  @media (max-width: 768px) {
    .split-screen {
      flex-direction: column;
    }
    
    .left-column,
    .right-column {
      flex: none;
    }
  }
  </style>
  