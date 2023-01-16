<template>
  <div class="button-container">
    <p class="optionNumber">{{ number + 1 }}</p>
    <button :ref="'option' + number + 1" @click="checkAnswer">
      {{ firstName + " " + lastName }}
    </button>
  </div>
</template>

<script>
export default {
  props: ["currentId", "firstName", "lastName", "thisId", "number", "keyPress"],
  data() {
    return {
      state: null,
    };
  },
  methods: {
    checkAnswer() {
      if (this.thisId === this.currentId) {
        this.$emit("some-event", "correct");
      } else {
        this.$emit("some-event", "incorrect");
      }
    },
  },
  watch: {
    keyPress() {
      if (this.number + 1 === this.keyPress) {
        this.checkAnswer();
      }
    },
  },
};
</script>

<style>
button {
  cursor: pointer;
  background: #fff;
  border: 1px solid #ccc;
  padding: 0.75rem 1rem;
  font-size: 1rem;
  font-family: "Inter", sans-serif;
  border-radius: 6px;
  font-weight: 400;
  color: #0f3951;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.05);
}

.button-container {
  position: relative;
  margin: 0.5rem;
}

button:hover {
  background-color: #f3f6f8;
}

.optionNumber {
  position: absolute;
  width: 1rem;
  height: 1rem;
  border: 1px solid #bbb;
  border-radius: 3px;
  transform: translateX(-50%);
  color: #111;
  line-height: 1.5em;
  left: 50%;
  top: 120%;
  font-size: 0.65rem;
  font-weight: 600;
  margin: 0.35rem;
  opacity: 0.6;
  box-shadow: 0 2px 0 #bbb;
}

@media (max-width: 480px) {
  .button-container {
    margin: 0;
  }

  button:hover {
    background-color: #fff;
  }
  .optionNumber {
    display: none;
  }
  button {
    width: 100%;
    margin: 0;
    border-radius: 0;
    padding: 1rem;
    border-width: 0 1px 0 1px;
    font-size: 1.1rem;
  }
  .button-container:first-child button {
    border-width: 1px;
    border-radius: 6px 6px 0 0;
  }
  .button-container:last-child button {
    border-width: 1px;
    border-radius: 0 0 6px 6px;
  }
  button:active {
    box-shadow: none;
  }
}
</style>
