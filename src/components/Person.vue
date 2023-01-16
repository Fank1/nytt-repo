<template>
  <div :ref="id" :class="state + ' ' + getResult + ' person-container'">
    <img :src="this.getImage" alt="" />
  </div>
</template>

<script>
export default {
  data() {
    return {
      count: 0,
    };
  },
  props: ["id", "staff", "state", "lastResult", "currentPersonId", "answered"],
  computed: {
    getImage() {
      let correctPerson = this.staff.find((person) => person.id === this.id);
      return correctPerson.imgSrc;
    },
    getResult() {
      let temp = this.answered.find((person) => person.id === this.id);
      if (temp) {
        return temp.result;
      } else {
        return "";
      }
    },
  },
};
</script>

<style>
.person-container {
  position: relative;
  max-width: 100px;
  margin: 0 auto;
  flex-shrink: 0;
}

.is-current {
  z-index: 3;
}

/* ---- RESULT OVERLAY ------ */

.person-container:last-child img {
  margin-right: 110px;
}

.person-container:after {
  opacity: 0;
  content: "";
  position: absolute;
  top: 0;
  bottom: 2px;
  left: 0;
  right: 0;
  border-radius: 100%;
  /*transition: all 200ms;*/
  box-shadow: inset 0 0 0px 2px #ffffff;
}

@keyframes push {
  0% {
    left: -10px;
  }
  50% {
    left: -20px;
  }
  100% {
    left: 0;
  }
}

.incorrect,
.correct {
  animation: push 200ms ease-in;
}

.incorrect:after,
.correct:after {
  font-size: 0.8rem;
  line-height: 1750%;
  font-weight: 700;
  letter-spacing: 0.06em;
}

.incorrect:after {
  content: "❌";
  color: hsl(0, 80%, 60%);
  opacity: 1;
  border: 3px solid hsl(0, 80%, 60%);
}

.correct:after {
  content: "✅";
  color: hsl(100, 60%, 40%);
  opacity: 1;
  border: 3px solid hsl(100, 60%, 50%);
}

.is-current img {
  /*transition: all 200ms;*/
  transform: translate(0, 0) scale(1.5);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
  filter: none;
}

.is-current + div {
  margin-right: 40px;
}
.is-current + div:before {
  opacity: 0;
}

img {
  width: 100%;
  border-radius: 100%;
  transition: all 200ms;
}
</style>
