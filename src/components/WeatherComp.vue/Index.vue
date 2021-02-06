<template>
  <div class="weather-comp">
    <div v-if="daily">
      <div v-if="city">{{ city.adm2 }} {{ city.name }}</div>
      <div class="flex">
        <div
          v-for="({ tempMax, tempMin, iconDay, fxDate, textDay, textNight },
          index) in daily"
          :key="fxDate"
        >
          <div>{{ getDayDesc(index) }}</div>
          <div>{{ getTmpDesc(tempMax, tempMin) }}</div>
          <img :src="require('./128/' + iconDay + '.png')" alt="logo.png" />
          <div>{{ getTqwDesc(textDay, textNight) }}</div>
        </div>
      </div>
      <img src="/static/128/100.png" alt="" />
    </div>
  </div>
</template>

<script>
const weatherApi = `https://devapi.qweather.com/v7/weather/3d`;
const cityApi = `https://geoapi.qweather.com/v2/city/lookup`;
const key = `d4ca037cd1f34249958320ebb31b42fb`

export default {
  name: "WeatherComp",
  props: {
    sensor: {
      type: Array,
      default() {
        return [0, 0];
      },
    },
  },
  data() {
    return {
      city: null,
      daily: null,
    };
  },
  computed: {},
  watch: {
    sensor: {
      immediate: true,
      async handler([lat, lon]) {
        console.log(lat, lon);
        this.getCity();
        this.getWeather();
      },
    },
  },
  methods: {
    getDayDesc(index) {
      if (index === 0) {
        return "今天";
      }
      return (
        "周" +
        ["一", "二", "三", "四", "五", "六", "日"][
          (new Date().getDay() - 1 + index) % 7
        ]
      );
    },
    getTmpDesc(min, max) {
      return `${max}℃/${min}℃`;
    },

    getTqwDesc(d, n) {
      if (d === n) {
        return d;
      }
      return `${d}转${n}`;
    },
    async getWeather() {
      const { data } = await this.$http.get(weatherApi, {
        params: {
          location: this.sensor.join(","),
          key,
        },
      });
      console.log(data);
      if (data.code !== "200") {
        console.error("天气接口异常");
      }
      this.daily = data.daily;
    },
    async getCity() {
      const { data } = await this.$http.get(cityApi, {
        params: {
          key,
          location: this.sensor.join(","),
        },
      });
      if (data.code !== "200") {
        console.error("城市接口异常");
      }
      this.city = data.location[0];
    },
  },
};
</script>

<style scoped lang="less">
.flex {
  display: flex;
  justify-content: space-between;
}
</style>
