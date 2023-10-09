<template>
  <div class="main_container">
    <List/>
  </div>
</template>

<script>
import List from './components/List.vue';
export default {
  name: 'App',
  components: {List}
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@300;400;500;600;700;800;900&display=swap');

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: 'Montserrat', sans-serif;
  color: #FFFFFF;
}

.main_container {
  display: flex;
  flex-direction: row;
  justify-content: space-around;
  height: 100vh;
}

</style>
