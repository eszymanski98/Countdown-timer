<template>
  <div class="home">
    <div class="mode-toggle" @click="modeToggle" :class="darkDark">
      <div class="toggle">
        <div id="dark-mode" type="checkbox"></div>
      </div>
    </div>
    <form class="input__container">
      <input
        type="datetime-local"
        class="input__date"
        step="1"
        v-model="inputDate"
        required
      />
      <button class="counter__button" @click="handleOnClick()">
        <span class="text">Create timer</span><span>Created!</span>
      </button>
    </form>
    <ul class="counter__list">
      <li v-for="(item, index) in counters" :key="index">
        <Counter
          :ref="setItemRef"
          :year="item.year"
          :month="item.month"
          :day="item.day"
          :hour="item.hour"
          :minute="item.minute"
          :second="item.second"
          :onExpire="() => handleOnExpire(index)"
        />
      </li>
    </ul>
  </div>
</template>

<script>
import Counter from "@/components/Counter.vue";

export default {
  name: "App",
  components: {
    Counter,
  },
  data: () => ({
    counters: [],
    inputDate: "",
    darkMode: false,
    itemRefs: [],
    timer: null,
  }),
  mounted() {
    if (localStorage.counters) {
      this.counters = JSON.parse([localStorage.counters]);
    }
    if (localStorage.inputDate) {
      this.inputDate = localStorage.inputDate;
    }
    this.timer = setInterval(() => {
      for (let i in this.itemRefs) {
        if (this.itemRefs[i]) {
          this.itemRefs[i].calculateRemaining();
        } else {
          this.itemRefs.splice(i, 1);
        }
      }
    }, 1000);
  },
  watch: {
    counters(newCounter) {
      localStorage.counters = JSON.stringify(newCounter);
    },
    inputDate(newDate) {
      localStorage.inputDate = newDate;
    },
  },
  methods: {
    setItemRef(el) {
      this.itemRefs.push(el);
    },
    handleOnClick() {
      const [year, month, day, hour, minute, second] =
        this.inputDate.split(/[-T:]/);
      let monthToInt = parseInt(month);
      monthToInt--;
      let monthToStr = monthToInt.toString();
      const now = new Date();
      const end = new Date(year, monthToStr, day, hour, minute, second);
      if (now.getTime() - end.getTime() <= 0) {
        this.counters.push({
          year,
          month: monthToStr,
          day,
          hour,
          minute,
          second,
        });
        this.counters = [...this.counters];
      }
    },
    handleOnExpire(index) {
      this.counters = [...this.counters];
      return this.counters.splice(index, 1);
    },
    dark() {
      document.querySelector("body").classList.add("dark-mode");
      document.querySelector(".input__date").classList.add("dark-mode");
      document.querySelector(".counter__button").classList.add("dark-mode");
      this.darkMode = true;
      this.$emit("dark");
    },

    light() {
      document.querySelector("body").classList.remove("dark-mode");
      document.querySelector(".input__date").classList.remove("dark-mode");
      document.querySelector(".counter__button").classList.remove("dark-mode");
      this.darkMode = false;
      this.$emit("light");
    },

    modeToggle() {
      if (
        this.darkMode ||
        document.querySelector("body").classList.contains("dark-mode")
      ) {
        this.light();
      } else {
        this.dark();
      }
    },
  },
  computed: {
    darkDark() {
      return this.darkMode && "darkmode-toggled";
    },
  },
};
</script>
<style lang="scss">
@import url("https://fonts.googleapis.com/css2?family=Source+Sans+Pro&display=swap");
$font-stack: "Source Sans Pro", sans-serif;
$dark: #171717;
$mode-toggle-bg: #262626;

body {
  background-color: #fff;
  color: $dark;
  transition: background-color 0.2s ease, color 0.2s ease;
}

body {
  transition: all 600ms cubic-bezier(0.48, 0, 0.12, 1);
  &.dark-mode {
    background-color: lighten($dark, 5%);
    .flex {
      .counter__list,
      .counter__button {
        color: #fff;
      }
    }
  }
}

.mode-toggle {
  position: relative;
  margin: auto 2% 0.5rem auto;
  padding: 0;
  width: 44px;
  height: 24px;
  min-width: 36px;
  min-height: 20px;
  background-color: $mode-toggle-bg;
  border: 0;
  border-radius: 24px;
  outline: 0;
  overflow: hidden;
  cursor: pointer;
  z-index: 2;
  -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
  -webkit-touch-callout: none;
  appearance: none;
  transition: background-color 0.5s ease;

  .toggle {
    position: absolute;
    top: 0;
    left: 0;
    bottom: 0;
    margin: auto;
    width: 20px;
    height: 20px;
    border-radius: 50%;
    border: 3px solid transparent;
    box-shadow: inset 0 0 0 2px #a5abba;
    overflow: hidden;
    transition: transform 0.5s ease;

    #dark-mode {
      position: relative;
      width: 100%;
      height: 100%;
      overflow: hidden;
      border-radius: 50%;

      &:before {
        content: "";
        position: relative;
        width: 100%;
        height: 100%;
        left: 50%;
        float: left;
        background-color: #a5abba;
        transition: border-radius 0.5s ease, width 0.5s ease, height 0.5s ease,
          left 0.5s ease, transform 0.5s ease;
      }
    }
  }
}

body.dark-mode {
  transition: all 600ms cubic-bezier(0.48, 0, 0.12, 1);
  .mode-toggle {
    background-color: lighten($mode-toggle-bg, 5%);

    .toggle {
      transform: translateX(19px);

      #dark-mode {
        &:before {
          border-radius: 50%;
          width: 150%;
          height: 85%;
          left: 40%;
          transform: translate(-10%, -40%), rotate(-35deg);
        }
      }
    }
  }
}

html,
body {
  width: 100%;
  height: 100%;
}

body {
  margin: 0;
  font: $font-stack;
}

.input__date {
  margin-right: 2%;
  border-style: none;
  background-color: white;
  color: black;
  height: 50px;
  text-align: center;
  border: solid 3px #5b42f3;
  font-size: 20px;
  transition: all 600ms cubic-bezier(0.48, 0, 0.12, 1);
}

.input__date.dark-mode {
  color: white;
  background-color: #18181a;
  transition: all 600ms cubic-bezier(0.48, 0, 0.12, 1);
}

.input__date::-webkit-calendar-picker-indicator {
  filter: invert(0);
  cursor: pointer;
  transition: all 600ms cubic-bezier(0.48, 0, 0.12, 1);
}

.input__date.dark-mode::-webkit-calendar-picker-indicator {
  filter: invert(1);
  cursor: pointer;
  transition: all 600ms cubic-bezier(0.48, 0, 0.12, 1);
}

.counter__list,
.counter__button {
  font-weight: 400;
}

.counter__button {
  position: relative;
  overflow: hidden;
  border: 1px solid #18181a;
  color: #18181a;
  display: inline-block;
  font-size: 20px;
  font-weight: 700;
  line-height: 15px;
  padding: 18px 18px 17px;
  text-decoration: none;
  cursor: pointer;
  background: #fff;
  user-select: none;
  -webkit-user-select: none;
  touch-action: manipulation;
  border: solid 3px #5b42f3;
  transition: background-color 600ms cubic-bezier(0.48, 0, 0.12, 1);
}

.counter__button.dark-mode {
  background: #18181a;
  color: white;
}

.counter__button span:first-child {
  position: relative;
  transition: color 600ms cubic-bezier(0.48, 0, 0.12, 1);
  z-index: 10;
}

.counter__button span:last-child {
  color: white;
  display: block;
  position: absolute;
  bottom: 0;
  transition: all 600ms cubic-bezier(0.48, 0, 0.12, 1);
  z-index: 100;
  opacity: 0;
  top: 65%;
  left: 50%;
  transform: translateY(225%) translateX(-50%);
  height: 14px;
  line-height: 13px;
}

.counter__button:after {
  content: "";
  position: absolute;
  bottom: -50%;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: #5b42f3;
  transform-origin: bottom center;
  transition: transform 600ms cubic-bezier(0.48, 0, 0.12, 1);
  transform: skewY(9.3deg) scaleY(0);
  z-index: 50;
}

.counter__button:focus:after {
  transform-origin: bottom center;
  transform: skewY(9.3deg) scaleY(2);
}

.counter__button:focus span:last-child {
  transform: translateX(-50%) translateY(-100%);
  opacity: 1;
  transition: all 600ms cubic-bezier(0.48, 0, 0.12, 1);
}

.counter__list {
  margin: auto;
  display: flex;
  flex-direction: column;
  justify-content: center;
  list-style-type: none;
}

.flex {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  width: 100%;
  height: 100%;
}
.input__container {
  display: flex;
  flex-direction: row;
  justify-content: center;
  margin: auto;
  margin-bottom: 2%;
}
</style>
