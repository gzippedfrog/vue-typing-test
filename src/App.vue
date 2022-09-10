<template>
  <h1>Test your typing speed</h1>
  <TypingTestCard
    :text="text"
    :loading="loading"
    @restart="fetchText"
    :key="text"
  />
</template>

<script>
import TypingTestCard from "./components/TypingTestCard.vue";

export default {
  name: "App",
  components: { TypingTestCard },
  data() {
    return {
      text: "",
      loading: false,
    };
  },
  methods: {
    fetchText() {
      this.loading = true;
      fetch("https://baconipsum.com/api/?type=all-meat&sentences=3&format=text")
        .then((res) => res.text())
        .then((data) => {
          this.text = data;
        })
        .catch(console.log)
        .finally(() => {
          this.loading = false;
        });
    },
  },
  mounted() {
    this.fetchText();
  },
};
</script>

<style lang="scss">
@import "node_modules/bootstrap/scss/bootstrap";
@import url("https://fonts.googleapis.com/css2?family=Roboto&display=swap");

#app {
  font-family: Roboto, Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  background-color: $gray-300;
  height: 100%;
  padding: 60px 10px 0;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  align-items: center;
}
</style>
