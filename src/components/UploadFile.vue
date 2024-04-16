<template>
    <div class="file-upload">
          <input type="file" @change="handleFileChange" ref="fileInput" />
          <button @click="uploadFile">Check</button>
        </div>
    <div class="split-screen">
      <div class="left-column">
        <div v-if="file1">
          <h2>File Raw</h2>
          <iframe :src="fileUrl1" width="100%" height="500px" frameborder="0"></iframe>
        </div>
        
      </div>
      <div class="right-column">
        <div v-if="file1">
          <h2>File Detect</h2>
          <iframe :src="fileUrl1" width="100%" height="500px" frameborder="0"></iframe>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    name: 'SplitScreen',
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
        if (!this.file1) {
          this.file1 = event.target.files[0];
          this.fileUrl1 = URL.createObjectURL(this.file1);
        } else if (!this.file2) {
          this.file2 = event.target.files[0];
          this.fileUrl2 = URL.createObjectURL(this.file2);
        } else {
          alert("You can only upload two files.");
        }
      },
      uploadFile() {
        this.fileUrl2 = URL.createObjectURL(this.file1);
        alert('11111')
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
  