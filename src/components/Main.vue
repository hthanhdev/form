<template>
  <main>
    <h2>Main Content</h2>
    <UploadFile></UploadFile>
  </main>
</template>


<script>
import UploadFile from './UploadFile.vue';

export default {
  name: 'MainContent', 
  components:  {
    UploadFile
  }
};

</script>

<style scoped>
main {
  padding: 10px;
  width: 95vw;
}
</style>
