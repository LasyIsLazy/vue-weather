<template>
  <div class="weather-comp">
    <div v-if="weather">
      {{ city }}
      <div class="flex">
        <div
          v-for="({ tmp_max, tmp_min, tqw_txt_d, tqw_txt_n },
          index) in forecast"
        >
          <div>{{ getDayDesc(index) }}</div>
          <div>{{ getTmpDesc(tmp_max, tmp_min) }}</div>
          <div>{{ getTqwDesc(tqw_txt_d, tqw_txt_n) }}</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
const getIpUrlBase = "https://api.aidioute.cn/ip/";
const getLocationInfoUrlBase =
  "https://search.heweather.net/find?key=0e6b2177d7f3421d8495e805eef57c73&group=cn&lang=zh&location=";
const getWeatherUrlBase =
  "https://apip.weatherdt.com/plugin/data?key=6gpFegk3V9&lang=zh&location=";

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
      position: null,
      weather: null,
    };
  },
  computed: {
    city() {
      return this.position.city;
    },
    forecast() {
      return this.weather.forecast.forecast;
    },
  },
  watch: {
    sensor: {
      immediate: true,
      async handler([lat, lon]) {
        console.log(lat, lon);
        await this.getCode();
        if (!this.position) {
          return;
        }
        await this.getWeather();
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
          new Date().getDay() - 1 + index
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
    async getCode() {
      let getCodeUrl = `${getLocationInfoUrlBase}${this.sensor[0]},${this.sensor[1]}`;
      const resultData = await this.$http.get(getCodeUrl);
      console.log("resultData", resultData);
      if (resultData.status === 200) {
        if (resultData.data.HeWeather6[0].status === "ok") {
          this.position = {
            code: resultData.data.HeWeather6[0].basic[0].cid.match(/\d+/)[0],
            province: resultData.data.HeWeather6[0].basic[0].admin_area,
            city: resultData.data.HeWeather6[0].basic[0].parent_city,
            area: resultData.data.HeWeather6[0].basic[0].location,
          };
        } else {
          console.log("获取位置失败");
          this.location = null;
        }
      } else {
        console.log("获取位置信息时网络错误");
        this.location = null;
      }
    },
    async getWeather() {
      const url = `${getWeatherUrlBase}${this.position.code}`;
      const weather = await this.$http.get(url);
      if (weather.status === 200) {
        if (weather.data.status === "ok") {
          // this.weather = {
          //   ...weather.data.now,
          //   aqi: weather.data.aqi.aqi,
          //   aqi_txt: weather.data.aqi.txt,
          // };
          this.weather = weather.data;
        } else {
          console.log("获取天气失败。");
        }
      } else {
        console.log("网络错误。");
      }
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
