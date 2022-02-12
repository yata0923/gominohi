<template>
  <img alt="Vue logo" src="./assets/logo.png" />
  <hr />
  今日のごみ
  <input type="text" :value="todayDate" />
  <input type="text" :value="todayGomi" /><br />
  明日のごみ
  <input type="text" :value="tomorrowDate" />
  <input type="text" :value="tomorrowGomi" /><br />
</template>

<script lang="ts">
import { defineComponent } from "vue";

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
    this.fetchGomi();
    this.getToday();
    this.getTomorrow();
  },

  methods: {
    dateToString(date: Date): string {
      const weekOfDay = ["日", "月", "火", "水", "木", "金", "土"];
      const mon = date.getMonth() + 1;
      const dat = date.getDate();
      const day = date.getDay();
      const week = weekOfDay[day];
      return mon + "/" + dat + " (" + week + ")";
    },
    
    async fetchGomi() {
      const response = await fetch('./gomi.json')
      const gomidata = await response.json();
      const date = new Date();
      const shonandai = gomidata['Shonandai'];
      const gomi = this.isFirstWeek(date) ? shonandai.gomiWeek1 : shonandai.gomiWeek2;
      this.todayGomi = gomi[date.getDay()];
      date.setDate(date.getDate() + 1);
      const gomiTomorrow = this.isFirstWeek(date) ? shonandai.gomiWeek1 : shonandai.gomiWeek2;
      this.tomorrowGomi = gomiTomorrow[date.getDay()];

    },

    isFirstWeek(date: Date) {
      const dayms = 24 * 60 * 60 * 1000;
      const weekTime = 7 * dayms;
      const offset = 4 * dayms;
      const time = date.getTime() + offset;
      return Math.floor(time / weekTime) % 2 === 0;
    },

    getToday: function () {
      const date = new Date();
      this.todayDate = this.dateToString(date);
      console.log(this.todayDate);
    },

    getTomorrow: function () {
      const tomorrow = new Date();
      // https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Date/setDate とのことなので以下で OK
      tomorrow.setDate(tomorrow.getDate() + 1);
      this.tomorrowDate = this.dateToString(tomorrow);
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
