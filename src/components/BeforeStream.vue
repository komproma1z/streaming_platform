<template>
  <div
    class="d-flex flex-column justify-end before-stream"
    :style="{backgroundImage:`url(${imageSrc})`}"
  >
    <div class="time-and-volume">
      <h2>До начала трансляции: {{ timeLeft }}</h2>
      <h3>Громкость: {{volume}}%</h3>
    </div>
    <div class="d-flex align-center qr">
      <h2>Управляй трансляцией с телефона</h2>
      <img class="arrow" src="../assets/triple-arrow.jpg" />
      <img src="../assets/qr.jpg" />
    </div>
  </div>
</template>

<script>
export default {
  name: "BeforeStream",
  props: ["getCurrentStream", "volume", "fetchData"],
  data() {
      return {
        currentTime: this.$moment().toISOString(),
        fetching: null,
      };
    },
  methods: {
    getTimeLeft: function(currentTime, streamTime) {
      let timeLeft = this.$moment(streamTime).diff(currentTime);

      // <логика склонения слов "час" и "минута">
      let minutesWord;
      let hoursWord;
      const elevenToNineteen = Array.from({length:9},(v,k)=>k+11).map(num => num.toString())
      const formatted = this.$moment.duration(timeLeft);
      let minutesString = formatted.minutes().toString();
      let hoursString = (formatted.hours()+24*formatted.days()).toString();
      if (elevenToNineteen.includes(minutesString.substr(-2))) {
        minutesWord = 'минут'
      } else {
        switch (minutesString[minutesString.length-1]) {
          case '1': minutesWord = 'минута';
          break;
          case ('234'.includes(minutesString[minutesString.length-1])): minutesWord = 'минуты';
          break;
          default: minutesWord = 'минут';
          break;
        }
      }
      if (elevenToNineteen.includes(hoursString.substr(-2))) {
        hoursWord = 'часов'
      } else {
        switch (hoursString[hoursString.length-1]) {
          case '1': hoursWord = 'час';
          break;
          case ('234'.includes(hoursString[hoursString.length-1])): hoursWord = 'минуты';
          break;
          default: hoursWord = 'часов';
          break;
        }
      }
      // </логика склонения слов "час" и "минута">

      return `${formatted.hours()+24*formatted.days()} ${hoursWord} ${formatted.minutes()} ${minutesWord} ${formatted.seconds()}`;
    },
  },
  created() {
    this.fetching = setInterval(() => this.fetchData(), 1000);
  },
  beforeDestroy() {
    clearInterval(this.fetching);
  },
  computed: {
    imageSrc: {
      get: function() {
        return this.getCurrentStream().image;
      }
    },
    timeLeft: {
      get: function() {
        return this.getTimeLeft(this.currentTime, this.getCurrentStream().started_at);
      }
    }
  }
};
</script>

<style lang="scss" scoped>
.before-stream {
  height: 100%;
  background-size: cover;
}

.time-and-volume {
  max-width: 600px;
  color: white;
  background: linear-gradient(90deg,rgba(0, 0, 0, 1) 0%,rgba(0, 0, 0, 1) 26%,rgba(0, 0, 0, 0.1825105042016807) 71%);
  padding: 5px 15px;
}

.qr {
  padding: 20px 60px;

  h2 {
    width: min-content;
  }

  * {
    margin-right: 25px;
  }
}

.arrow {
  height: 80px;
}
</style>