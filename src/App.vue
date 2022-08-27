<template>
  <h1>Test your typing speed</h1>
  <TypingTestCard :text="text" @restart="fetchText" :key="text" />
</template>

<script>
import TypingTestCard from "./components/TypingTestCard.vue";

export default {
  name: "App",
  data() {
    return {
      text: "Lorem ipsum dolor sit amet consectetur adipisicing elit. Nulla mollitia vitae voluptatem necessitatibus accusantium laboriosam rerum aspernatur? Repudiandae voluptatum ducimus quod, perspiciatis nisi esse porro doloribus, consequuntur voluptas aliquam incidunt excepturi.",
    };
  },
  components: { TypingTestCard },
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
  padding-top: 60px;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  align-items: center;
}
</style>
