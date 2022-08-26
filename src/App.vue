<template>
  <p ref="text">
    <span v-for="[char, i] in chars" :key="i"> {{ char }} </span>
  </p>

  <p>Errors: {{ errors }}</p>
  <p v-if="typedLength">Accuracy: {{ accuracy }}%</p>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      text: "Lorem ipsum dolor sit amet consectetur adipisicing elit.",
      typedChars: [],
      currentIndx: 0,
      errors: 0,
    };
  },
  computed: {
    chars() {
      return this.text.split("");
    },
    charElems() {
      return this.$refs.text.children;
    },
    typedLength() {
      return this.typedChars.length;
    },
    accuracy() {
      const percents =
        ((this.typedLength - this.errors) / this.typedLength) * 100;
      return Math.floor(percents);
    },
  },
  methods: {
    handleKeyDown(e) {
      if (e.key === "Backspace" && this.currentIndx > 0) {
        this.typedChars.pop();
        this.currentIndx--;
      } else if (e.key.length === 1 && this.typedLength < this.text.length) {
        this.typedChars.push(e.key);
        this.currentIndx++;
      }
    },
  },
  mounted() {
    window.onkeydown = this.handleKeyDown;
    this.charElems[this.currentIndx].className = "current";
  },
  unmounted() {
    window.onkeydown = null;
  },
  watch: {
    typedChars: {
      handler(typedChars) {
        this.errors = 0;
        typedChars.forEach((typedChar, i) => {
          const charEl = this.$refs.text.children[i];
          if (typedChar === this.text[i]) {
            charEl.classList.add("success");
          } else {
            charEl.classList.add("error");
            this.errors++;
          }
        });
      },
      deep: true,
    },
    currentIndx(i, prevI) {
      if (this.charElems[i]) {
        this.charElems[i].className = "current";
      }
      this.charElems[prevI]?.classList.remove("current");
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin: 60px auto 0;
  max-width: 500px;
  font-weight: bold;
}

.success {
  color: green;
}

.error {
  color: red;
}

.current {
  background-color: orange;
}
</style>
