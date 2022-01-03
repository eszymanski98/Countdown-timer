<template>
  <div class="counter__container">
    <div class="time__container">
      <div class="time">{{ displayDays }}<span class="separator">:</span></div>
      <div class="text__under">days</div>
    </div>
    <div class="time__container">
      <div class="time">{{ displayHours }}<span class="separator">:</span></div>
      <div class="text__under">hours</div>
    </div>
    <div class="time__container">
      <div class="time">
        {{ displayMinutes }}<span class="separator">:</span>
      </div>
      <div class="text__under">minutes</div>
    </div>
    <div class="time__container">
      <div class="time">
        {{ displaySeconds }}<span class="separator"></span>
      </div>
      <div class="text__under">seconds</div>
    </div>
    <button class="button__rm__timer" @click="handleExpire()">
      <span class="text">Remove timer</span>
    </button>
  </div>
</template>

<script>
export default {
  props: ["year", "month", "day", "hour", "minute", "second", "onExpire"],
  data: () => ({
    displayDays: 0,
    displayHours: 0,
    displayMinutes: 0,
    displaySeconds: 0,
    count: 1,
    expired: false,
  }),
  computed: {
    _seconds: () => 1000,
    _minutes() {
      return this._seconds * 60;
    },
    _hours() {
      return this._minutes * 60;
    },
    _days() {
      return this._hours * 24;
    },
    end() {
      return new Date(
        this.year,
        this.month,
        this.day,
        this.hour,
        this.minute,
        this.second
      );
    },
  },
  mounted() {
    this.calculateRemaining();
  },
  updated() {
    this.calculateRemaining();
  },
  methods: {
    handleExpire() {
      this.onExpire();
    },
    formatNum: (num) => (num < 10 ? "0" + num : num),
    calculateRemaining() {
      const now = new Date();
      const distance = this.end.getTime() - now.getTime();

      if (distance < 0) {
        this.expired = true;
        this.handleExpire();
      }
      const days = Math.floor(distance / this._days);
      const hours = Math.floor((distance % this._days) / this._hours);
      const minutes = Math.floor((distance % this._hours) / this._minutes);
      const seconds = Math.floor((distance % this._minutes) / this._seconds);
      this.displayMinutes = this.formatNum(minutes);
      this.displaySeconds = this.formatNum(seconds);
      this.displayHours = this.formatNum(hours);
      this.displayDays = this.formatNum(days);
    },
  },
};
</script>

<style lang="scss">
@media (min-width: 768px) {
  .button-64 {
    font-size: 24px;
    min-width: 196px;
  }
}
@mixin gradient-text($angle: 45deg, $color: rgb(64, 45, 235), $amount: 35%) {
  color: $color;
  background: -webkit-linear-gradient(
    $angle,
    $color,
    adjust-hue($color, $amount)
  );
  -webkit-text-fill-color: transparent;
  -webkit-background-clip: text;
  display: inline-block;
}

.counter__container {
  display: flex;
  flex-direction: row;
  margin: auto;
  max-width: 100%;
  align-items: center;
  justify-content: center;
}

.time {
  font-size: 70px;
}

.text__under {
  font-size: 15px;
  text-transform: uppercase;
  font-weight: 700;
}

.time__container {
  @include gradient-text;
  padding-right: 0.1%;
  padding-left: 0.1%;
  margin-right: 0.5%;
}

.time__container:last-child {
  margin-right: 5%;
}

.button__rm__timer {
  align-items: center;
  background-image: linear-gradient(144deg, #af40ff, #5b42f3 50%, #00ddeb);
  border: 0;
  border-radius: 8px;
  box-shadow: rgba(151, 65, 252, 0.2) 0 15px 30px -5px;
  box-sizing: border-box;
  color: #ffffff;
  display: flex;
  font-family: Phantomsans, sans-serif;
  font-size: 15px;
  justify-content: center;
  line-height: 1em;
  max-width: 100%;
  min-width: 120px;
  padding: 3px;
  text-decoration: none;
  user-select: none;
  -webkit-user-select: none;
  touch-action: manipulation;
  white-space: nowrap;
  cursor: pointer;
  margin-left: 1%;
}

.button__rm__timer:active,
.button__rm__timer:hover {
  outline: 0;
}

.button__rm__timer span {
  background-color: rgb(5, 6, 45);
  padding: 16px 24px;
  border-radius: 6px;
  width: 100%;
  height: 100%;
  transition: 300ms;
}

.button__rm__timer:hover span {
  background: none;
}

.button__rm__timer:active span {
  background: rgb(212, 60, 60);
}

@media (min-width: 768px) {
  .button__rm__timer {
    font-size: 20px;
    min-width: 196px;
  }
}
</style>
