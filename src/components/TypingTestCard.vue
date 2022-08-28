<template>
  <div class="card text-bg-light mt-3 shadow-sm">
    <div class="card-body">
      <p v-show="loading" class="card-text placeholder-glow">
        <span class="placeholder col-4"></span>&nbsp;
        <span class="placeholder col-7"></span>&nbsp;
        <span class="placeholder col-2"></span>&nbsp;
        <span class="placeholder col-4"></span>&nbsp;
        <span class="placeholder col-3"></span>&nbsp;
        <span class="placeholder col-9"></span>&nbsp;
        <span class="placeholder col-2"></span>&nbsp;
        <span class="placeholder col-5"></span>&nbsp;
        <span class="placeholder col-5"></span>&nbsp;
      </p>

      <p v-show="!loading" class="card-text mono-text" ref="text">
        <span v-for="[char, i] in chars" :key="i"> {{ char }} </span>
      </p>

      <div class="card-text container">
        <div class="row">
          <div :class="[{ hidden: !typedLength }, 'col']">
            <div class="row fw-bold">Accuracy:</div>
            <div class="row">{{ accuracy }}%</div>
          </div>
          <div :class="[{ hidden: !typedLength }, 'col']">
            <div class="row fw-bold">Speed:</div>
            <div class="row">{{ typingSpeed }} chars/min</div>
          </div>
          <button
            type="button"
            class="btn btn-warning shadow-sm col"
            @click="restart"
          >
            Restart
            <svg
              xmlns="http://www.w3.org/2000/svg"
              width="16"
              height="16"
              fill="currentColor"
              class="bi bi-arrow-counterclockwise"
              viewBox="0 0 16 16"
            >
              <path
                fill-rule="evenodd"
                d="M8 3a5 5 0 1 1-4.546 2.914.5.5 0 0 0-.908-.417A6 6 0 1 0 8 2v1z"
              />
              <path
                d="M8 4.466V.534a.25.25 0 0 0-.41-.192L5.23 2.308a.25.25 0 0 0 0 .384l2.36 1.966A.25.25 0 0 0 8 4.466z"
              />
            </svg>
          </button>
        </div>
      </div>
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
  props: ["text", "loading"],
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
        !this.timerId && this.startTest();
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

<style lang="scss" scoped>
@import "node_modules/bootstrap/scss/bootstrap";
@import url("https://fonts.googleapis.com/css2?family=Roboto&display=swap");

.card {
  width: 500px;
}

.mono-text {
  font-family: "Roboto Mono", monospace;
  white-space: pre-wrap;
}

.hidden {
  opacity: 0;
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
