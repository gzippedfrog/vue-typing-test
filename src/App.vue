<template>
  <h1>Test your typing speed</h1>
  <TypingTestCard :text="text" @restart="fetchText" :loading="loading" :key="text" />
</template>

<script>
import TypingTestCard from "./components/TypingTestCard.vue";

export default {
  name: "App",
  data() {
    return {
      text: "Lorem ipsum dolor sit amet consectetur adipisicing elit. Nulla mollitia vitae voluptatem necessitatibus accusantium laboriosam rerum aspernatur? Repudiandae voluptatum ducimus quod, perspiciatis nisi esse porro doloribus, consequuntur voluptas aliquam incidunt excepturi.",
      loading: false,
    };
  },
  components: { TypingTestCard },
  methods: {
    fetchText() {
      this.loading = true;
      fetch("http://metaphorpsum.com/paragraphs/1/4")
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
