<template>
  <div class="intro" v-if="!hasStarted">
    <div class="content">
      <h2 class="heading">Antrops namnquiz</h2>
      <p>
        Hur väl kan du dina kollegors namn? Ju <em>snabbare</em> du svarar,
        desto mer poäng får du. Om du svarar fel eller inte är snabb nog, så får
        du <em>0 poäng</em>. På desktop kan du använda siffertangenterna för att
        svara extra snabbt!
      </p>
      <button
        @click="
          {
            this.hasStarted = true;
            startTimer();
          }
        "
      >
        ⚡️ Starta!
      </button>
    </div>
  </div>
  <div class="topbar">
    <div
      class="progress"
      :style="{ width: (this.currentPosition / this.staff.length) * 100 + '%' }"
    ></div>
    <div
      class="logo"
      @click="
        {
          stopTimer();
        }
      "
    ></div>
    <div class="righthand">
      <p v-if="!isFinished" class="score">
        <span class="number">{{ score }}</span> poäng
      </p>
      <!---<p>{{ timerTicker }}</p> -->
    </div>
  </div>
  <div class="wrappest">
    <div class="modal" v-if="this.isFinished">
      <h1>
        <span class="part">Du fick</span
        ><span class="part points">{{ this.score }}</span
        ><span class="part"> poäng</span>
      </h1>
      <button
        @click="
          {
            reset();
          }
        "
        class="restart"
      >
        ↻ Spela igen
      </button>
    </div>
    <Doughnuts
      :duration="duration"
      :timerTicker="timerTicker"
      :pieData="this.pieData()"
      v-if="!this.isFinished"
    />
    <div class="wrapper" ref="wrapper" v-if="!this.isFinished">
      <div class="people-container">
        <Person
          v-for="person in this.shuffleStaff"
          :staff="this.staff"
          :id="person"
          :currentPersonId="this.currentPersonId"
          :key="person.index"
          :state="this.currentPersonId === person ? 'is-current' : ''"
          :answered="this.answered"
        />
      </div>
    </div>
  </div>
  <h2 class="title" v-if="!this.isFinished">Vem är detta?</h2>
  <div class="options" v-if="!this.isFinished">
    <Option
      @some-event="answer"
      v-for="(option, index) in this.setRandomOptionIds"
      :currentId="this.currentPersonId"
      :thisId="option.id"
      :firstName="option.firstName"
      :lastName="option.lastName"
      :key="option.index"
      :number="index"
      :keyPress="this.keyPress"
    />
  </div>
</template>

<script>
import Person from "./components/Person.vue";
import Option from "./components/Option.vue";
import Doughnuts from "./components/Doughnuts.vue";
import StaffJson from "./staff.json";

export default {
  data() {
    return {
      staff: StaffJson,
      currentPosition: 0,
      currentPersonId: "",
      amountOfOptions: 3,
      answered: [],
      score: 0,
      lastResult: null,
      keyPress: null,
      timer: null,
      timerTicker: 0,
      duration: 6000,
      isFinished: false,
      hasStarted: false,
    };
  },
  components: {
    Person,
    Option,
    Doughnuts,
  },
  methods: {
    reset() {
      location.reload();
      return false;
    },
    pieData() {
      let object = {
        datasets: [
          {
            data: [this.duration - this.timerTicker, this.timerTicker],
            backgroundColor: ["transparent", "#2c3e50"],
            cutout: "96%",
          },
        ],
      };
      return object;
    },
    startTimer() {
      this.timerTicker = this.duration;
      this.timer = setInterval(() => {
        this.timerTicker -= 10;
        if (this.timerTicker <= 0) {
          this.stopTimer();
          this.answer("incorrect");
        }
      }, 10);
    },
    stopTimer() {
      clearInterval(this.timer);
      this.timer = null;
    },
    setFinished() {
      this.isFinished = true;
      this.stopTimer();
    },
    answer(result) {
      console.log("klick");
      if (result === "correct") {
        this.score += Math.floor(this.timerTicker / 100);
        this.lastResult = "correct";
      } else {
        this.lastResult = "incorrect";
      }
      this.answered.push({
        id: this.currentPersonId,
        result: result,
      });
      this.currentPosition++;
      this.timerTicker++;
      this.keyPress = null;
    },
    handleKeyUp(e) {
      let numberVal = parseInt(e.key);
      //console.log(number);
      if (numberVal <= this.amountOfOptions && numberVal !== 0) {
        this.keyPress = numberVal;
      }
    },
  },
  computed: {
    shuffleStaff() {
      let currentIndex = this.staff.length,
        randomIndex;

      // While there remain elements to shuffle.
      while (currentIndex !== 0) {
        // Pick a remaining element.
        randomIndex = Math.floor(Math.random() * currentIndex);
        currentIndex--;

        // And swap it with the current element.
        [this.staff[currentIndex], this.staff[randomIndex]] = [
          this.staff[randomIndex],
          this.staff[currentIndex],
        ];
      }
      let temp = [];
      for (let i = 0; i < this.staff.length; i++) {
        temp.push(this.staff[i].id);
      }
      return temp;
    },
    setRandomOptionIds() {
      let temp = [];
      const currentPerson = this.staff.find(
        (person) => person.id === this.currentPersonId
      );
      for (let i = 0; i < this.staff.length; i++) {
        let randomIndex = Math.floor(Math.random() * this.staff.length);
        let isPersonInTemp = temp.find(
          (person) => person.id === this.staff[randomIndex].id
        );
        if (
          this.staff.find((person) => person.id === this.currentPersonId)
            .gender === this.staff[randomIndex].gender &&
          temp.length < this.amountOfOptions - 1 &&
          !isPersonInTemp &&
          currentPerson.id !== this.staff[randomIndex].id
        ) {
          temp.push(this.staff[randomIndex]);
        }
        // checking if this person has the same gender
      }
      let randomPosition = Math.floor(Math.random() * this.amountOfOptions);
      temp.splice(randomPosition, 0, currentPerson);
      return temp;
    },
  },
  created() {
    let self = this;
    this.currentPersonId = this.shuffleStaff[this.currentPosition];
    window.addEventListener("keyup", function (e) {
      self.handleKeyUp(e);
    });
  },
  watch: {
    currentPosition() {
      if (this.currentPosition < this.staff.length) {
        console.log(this.currentPosition);
        this.currentPersonId = this.shuffleStaff[this.currentPosition];
        this.$refs.wrapper.scrollLeft = this.currentPosition * 100;
        this.stopTimer();
        this.startTimer();
      } else {
        this.setFinished();
      }
    },
  },
  unmounted() {
    window.removeEventListener("keyup", function (e) {
      this.handleKeyUp(e);
    });
  },
  mounted() {
    //this.startTimer();
  },
};
</script>

<style>
#app {
  box-sizing: border-box;
  text-align: center;
  color: #2c3e50;
  margin: 0 16px;
}

.heading {
  font-family: inherit, sans-serif;
  font-size: 1.7rem;
}

.intro {
  margin: 16px;
  left: -16px;
  top: -16px;
  position: absolute;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  width: 100vw;
}

.intro:before {
  position: absolute;
  content: "";
  background-color: rgba(0, 0, 0, 0.4);
  width: 100%;
  height: 100%;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  z-index: 9999;
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
}

.intro .content {
  width: auto;
  margin: 24px;
  max-width: 480px;
  position: absolute;
  background-color: #fff;
  z-index: 99999;
  border-radius: 8px;
  padding: 24px;
  box-shadow: 0 12px 24px rgba(0, 0, 0, 0.2);
}

.intro .content button {
  background-color: #0f3951;
  border: none;
  color: #fff;
}

.intro p {
  margin: 16px 0;
  line-height: 1.5em;
}

.intro p em {
  font-style: normal;
  font-weight: 700;
}

.title {
  margin: 1rem 0;
  font-size: 1.5rem;
  letter-spacing: -0.02em;
  font-weight: 600;
}

.topbar {
  position: relative;
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-bottom: 1px solid #ddd;
  padding: 12px 0;
  font-variant-numeric: tabular-nums;
}

.progress {
  transition: width 300ms;
  position: absolute;
  background-color: #000;
  height: 1px;
  bottom: -1px;
}

.righthand {
  display: flex;
  align-items: center;
}
.score {
  color: #0f3951;
  font-weight: 700;
  margin-right: 0.5em;
}
.counter {
  color: #000;
  opacity: 0.6;
}

.logo {
  width: 32px;
  height: 32px;
  background: url("./assets/antrop-logo.svg");
  background-size: contain;
  background-repeat: no-repeat;
}

.wrappest {
  position: relative;
}

.wrapper {
  width: 310px;
  height: 220px;
  margin: 0 auto;
  overflow-x: hidden;
}

.people-container {
  position: relative;
  margin-top: 2.25rem;
  margin-right: 900px;
  margin-left: 100px;
  display: flex;
  align-items: center;
  height: 156px;
}

.options {
  margin-top: 1.5rem;
  display: flex;
  justify-content: center;
}

.modal {
  margin-top: 32px;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.modal h1 {
  display: flex;
  flex-direction: column;
}

.modal h1 .points {
  font-size: 3rem;
  margin: 0.5rem 0;
}

.modal p {
  margin-top: 1rem;
}

.modal button {
  max-width: 300px;
  background-color: #0f3951;
  border: none;
  color: #fff;
  margin-top: 24px;
}

#progressbar {
  position: absolute;
  width: 150px;
  height: 150px;
  z-index: 999;
  left: 50%;
  top: 50%;
  transform: translate(calc(-50% - 5px), calc(-50% + 3px));
  box-shadow: inset 0 0 0px 5px #ffffff;
  border-radius: 100%;
}

@media (max-width: 480px) {
  .options {
    flex-direction: column;
  }
}

@media (prefers-color-scheme: dark) {
  #App {
    background-color: red;
    color: #fff;
  }
}
</style>
