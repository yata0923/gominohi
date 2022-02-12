<template>
  <img alt="Vue logo" src="./assets/logo.png" />
  <hr />
  <!-- <select>
    <option>湘南台</option>
    <option>目黒区</option></select
  ><br /> -->

  今日のごみ
  <input type="date" v-model="todayDate" />
  <input type="text" :value="todayGomi" /><br />
  明日のごみ
  <input type="date" readonly :value="tomorrowDate" />
  <input type="text" :value="tomorrowGomi" /><br />
</template>

<script lang="ts">
import { defineComponent } from "vue";

function dateToString(date: Date): string {
  return date.toISOString().slice(0, 10);
}

export default defineComponent({
  name: "App",
  components: {},
  data: () => {
    return {
      todayDate: "",
      todayGomi: "",
      tomorrowDate: "",
      tomorrowGomi: "",
    };
  },
  mounted: function () {
    this.todayDate = dateToString(new Date());
  },
  watch: {
    todayDate: function (date: string): void {
      console.log(date);
      this.todayDate = date;
      this.setTomorrow();
      this.fetchGomi();
    },
  },
  methods: {
    async fetchGomi() {
      const response = await fetch("./gomi.json");
      const gomidata = await response.json();
      const date = new Date(this.todayDate);
      const shonandai = gomidata["Shonandai"];
      const gomi = this.isFirstWeek(date)
        ? shonandai.gomiWeek1
        : shonandai.gomiWeek2;
      this.todayGomi = gomi[date.getDay()];
      date.setDate(date.getDate() + 1);
      const gomiTomorrow = this.isFirstWeek(date)
        ? shonandai.gomiWeek1
        : shonandai.gomiWeek2;
      this.tomorrowGomi = gomiTomorrow[date.getDay()];
    },

    isFirstWeek(date: Date) {
      const dayms = 24 * 60 * 60 * 1000;
      const weekTime = 7 * dayms;
      const offset = 4 * dayms;
      const time = date.getTime() + offset;
      return Math.floor(time / weekTime) % 2 === 0;
    },

    setTomorrow: function () {
      const tomorrow = new Date(this.todayDate);
      // https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Date/setDate とのことなので以下で OK
      tomorrow.setDate(tomorrow.getDate() + 1);
      this.tomorrowDate = dateToString(tomorrow);
    },
  },
});
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
