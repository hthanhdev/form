<template>
  <div id="app">
    <Header />
    <div class="container">
      <Sidebar />
      <MainContent />
    </div>
    <Footer />
  </div>
</template>

<script>
import Header from './components/Header.vue';
import Sidebar from './components/Sidebar.vue';
import MainContent from './components/Main.vue';
import Footer from './components/Footer.vue';

export default {
  name: 'App',
  components: {
    Header,
    Sidebar,
    MainContent,
    Footer,
  },
};
</script>

<style>
.container {
  display: flex;
}
</style>
