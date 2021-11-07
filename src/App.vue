<template>
  <div id="app">
    <h2>Таймер обратного отсчёта</h2>
    <div class="circle">
      <diagram
        class-circle-front="yellow"
        :data-dasharray="[timePassed, timer.total]"
        radius="51%"
        :satellite="true"
        is-timer
      />
      <span>{{ time }}</span>
    </div>
    <br />
    <hr />
    <br />
    <h3>Продано фруктов на {{ fruits.total }} руб. из них:</h3>

    <div class="fruit-wrap">
      <div class="circle circle--static">
        <diagram
          class-circle-front="green"
          :data-dasharray="[fruits.apple, fruits.total]"
          radius="51%"
        />
        <span>Яблок&nbsp;на {{ fruits.apple }} руб.</span>
      </div>
      <div class="circle circle--static">
        <diagram
          class-circle-front="blue"
          :data-dasharray="[fruits.bananas, fruits.total]"
          radius="51%"
        />
        <span>Бананов&nbsp;на {{ fruits.bananas }} руб.</span>
      </div>
      <div class="circle circle--static">
        <diagram
          class-circle-front="red"
          :data-dasharray="[fruits.pear, fruits.total]"
          radius="51%"
        />
        <span>Груш&nbsp;на {{ fruits.pear }} руб.</span>
      </div>
    </div>
    <br />
  </div>
</template>

<script>
import Diagram from "./components/Diagram";

export default {
  name: "App",
  components: {
    Diagram,
  },
  data() {
    return {
      timer: {
        descriptor: undefined,
        minutes: 2,
        seconds: 0,
        total: 120000,
      },
      fruits: {
        apple: 0,
        bananas: 0,
        pear: 0,
        total: 1000,
      },
    };
  },
  computed: {
    time() {
      return `${this.timer.minutes}:${
        this.timer.seconds < 10 ? "0" + this.timer.seconds : this.timer.seconds
      }`;
    },
    timePassed() {
      const minutesInSeconds =
        this.timer.minutes !== 0 ? this.timer.minutes * 60 : 1;
      const passed =
        this.timer.total -
        (minutesInSeconds * 1000 + this.timer.seconds * 1000);
      return this.timer.total - passed;
    },
  },
  mounted() {
    setTimeout(() => {
      this.fruits.apple = 200;
      this.fruits.bananas = 280;
      this.fruits.pear = 350;
    }, 100);
    this.timer.descriptor = window.setInterval(this.runTimer, 1000);
  },
  methods: {
    runTimer() {
      if (this.timer.seconds === 0 && this.timer.minutes === 0) {
        window.clearInterval(this.timer.descriptor);
        this.timer.descriptor = undefined;
        this.timerLost = true;
        return;
      }

      if (this.timer.seconds === 0) {
        this.timer.seconds = 59;
        this.timer.minutes--;
      } else {
        this.timer.seconds--;
      }
    },
  },
};
</script>

<style lang="scss">
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #cac;
  margin-top: 20px;
  position: relative;
  background: #2c3e50;
  padding: 10px 0;
}

.fruit-wrap {
  display: flex;
  justify-content: space-between;
  padding: 0 5em;
}

.circle {
  position: relative;
  width: 180px;
  height: 180px;
  margin: 0 auto;
  border-radius: 50%;
  color: #fff;

  &--static {
    display: inline-block;
    margin: 0;
    width: 140px;
    height: 140px;
    position: relative;
    left: 0;

    span {
      font-size: 16px !important;
      font-weight: normal !important;
      /* z-index: -1; */
    }

    circle {
      /* stroke-width: 100% !important; */
      stroke-width: 3px !important;
      /* transform: rotateZ(90deg) rotateY(-180deg) scale(0.5) !important; */
      transform: rotateZ(90deg) rotateY(-180deg) !important;
      transition-duration: 1.5s !important;
      z-index: 2;
    }
  }

  span {
    display: block;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    opacity: 1;
    color: #fff;
    font-weight: bold;
    font-size: 24px;
    line-height: 25px;
  }

  .yellow {
    stroke: #ced105 !important;
  }
  .green {
    stroke: #00a323 !important;
  }
  .blue {
    stroke: #1d99ff !important;
  }
  .red {
    stroke: #920000 !important;
  }
}
</style>
