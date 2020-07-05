<template>
  <div id="app">
    <ContactList :api-address="apiAddress" />
  </div>
</template>

<script>
import ContactList from "./components/ContactList.vue";

export default {
  name: "App",
  data() {
    return {
      apiAddress: "https://localhost:2020/api/contacts"
    };
  },
  components: {
    ContactList
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
