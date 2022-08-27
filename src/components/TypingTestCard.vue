<template>
  <div class="card text-bg-light mt-3 shadow-sm">
    <div class="card-body">
      <p class="card-text" ref="text">
        <span v-for="[char, i] in chars" :key="i"> {{ char }} </span>
      </p>

      <p v-show="typedLength" class="card-text">
        <span class="row fw-bold">
          <span class="col">Accuracy: </span>
          <span class="col">Speed: </span>
        </span>
        <span class="row">
          <span class="col">{{ accuracy }}%</span>
          <span class="col">{{ typingSpeed }} chars/min</span>
        </span>
      </p>
      <button type="button" class="btn btn-warning shadow-sm" @click="restart">
        Restart
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: "TypingTestCard",
  data() {
    return {
      typedChars: [],
      currentIndx: 0,
      errors: 0,
      timerId: null,
      seconds: 0,
      completed: false,
    };
  },
  props: ["text"],
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
    typingSpeed() {
      const speed =
        ((this.typedLength - this.errors) * 60) / (this.seconds || 1);
      return Math.floor(speed);
    },
  },
  methods: {
    handleKeyDown(e) {
      if (this.completed) return;

      if (e.key === "Backspace" && this.currentIndx > 0) {
        this.typedChars.pop();
        this.currentIndx--;
      } else if (e.key.length === 1 && this.typedLength < this.text.length) {
        if (!this.timerId) {
          this.startTest();
        }
        this.typedChars.push(e.key);
        this.currentIndx++;
      }
    },
    startTest() {
      this.timerId = setInterval(() => {
        this.seconds++;
      }, 1000);
    },
    restart() {
      clearInterval(this.timerId);

      this.$emit("restart");
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
        this.errors = typedChars.reduce((errors, typedChar, i) => {
          const charEl = this.$refs.text.children[i];
          if (typedChar === this.text[i]) {
            charEl.className = "success";
            return errors;
          } else {
            charEl.className = "error";
            return errors + 1;
          }
        }, 0);
        if (this.typedLength == this.text.length) {
          clearInterval(this.timerId);
          this.timerId = null;
          this.completed = true;
        }
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

<style lang="scss">
@import "node_modules/bootstrap/scss/bootstrap";

.card {
  max-width: 500px;
}

.success {
  color: $green-600;
  background-color: $green-200;
}

.error {
  color: $red-600;
  background-color: $red-200;
}

.current {
  color: $yellow-600;
  background-color: $yellow-200;
}
</style>
