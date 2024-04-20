<template>
  <aside>
    <!-- <h2>Sidebar</h2>
    <nav>
      <ul>
        <li><a href="#">Link 1</a></li>
        <li><a href="#">Link 2</a></li>
        <li><a href="#">Link 3</a></li>
      </ul>
    </nav> -->
    <h3><a href="#">Check permission field</a></h3>
  </aside>
</template>

<script>
export default {
  name: 'SideBar', // Updated name to follow multi-word convention
};
</script>

<style scoped>
aside {
  background-color: #f4f4f4;
  padding: 10px;
}

nav ul {
  list-style-type: none;
}

nav ul li {
  margin-bottom: 5px;
}

nav ul li a {
  text-decoration: none;
  color: #333;
  display: block;
  padding: 5px;
  border-radius: 5px;
}

nav ul li a:hover {
  background-color: #ddd;
}
</style>
