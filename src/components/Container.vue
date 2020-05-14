<template>
  <v-app>
    <div v-if="!dataLoaded">Please wait</div>
    <div v-else style="height: 100%">
      <div class="iframe-wrapper" v-if="compareTime(currentTime, getCurrentStream().started_at)">
        <iframe width="100%" height="100%" :src="videoSrc"></iframe>
      </div>
      <BeforeStream 
        v-else
        :getCurrentStream="getCurrentStream"
        :getTimeLeft="getTimeLeft"
        :volume="volume"
        :imageSrc="imageSrc"
        :key="rerender_trigger"
      />
    </div>
  </v-app>
</template>

<script>
import BeforeStream from './BeforeStream';
export default {
  name: "Container",
      components: {
        BeforeStream
  },
  data() {
    return {
      streams: [],
      volume: null,
      deviceCurrentStreamID: null,
      currentTime: this.$moment().toISOString(),
      dataLoaded: false,
      fetching: null,
      rerender_trigger: 0,
    };
  },
  methods: {
    fetchData: function() {
      fetch("https://stream.crona.tech/api/streams/device/detail/?id=1")
        .then(res => res.json())
        .then(data => {
          this.rerender_trigger += 1;
          this.streams = data.data.streams;
          this.volume = JSON.parse(data.data.states[0].properties)["volume"];
          this.deviceCurrentStreamID = JSON.parse(data.data.states[0].properties)["current_stream"];
          this.dataLoaded = true;
        })
      .catch(err => err);
    },
    getCurrentStream: function() {
      return this.streams.filter(stream => stream.id === this.deviceCurrentStreamID)[0];
    },
    compareTime: function(currentTime, streamTime) {
      return currentTime > streamTime;
    },
    getTimeLeft: function(currentTime, streamTime) {
      let timeLeft = this.$moment(streamTime).diff(currentTime);
      let minutes_word;
      let hours_word;
      const formatted = this.$moment.duration(timeLeft);
      let minutes_string = formatted.minutes().toString();
      let hours_string = formatted.hours().toString();
      switch (minutes_string[minutes_string.length-1]) {
        case '1': minutes_word = 'минута';
        break;
        case '2': minutes_word = 'минуты';
        break;
        case '3': minutes_word = 'минуты';
        break;
        case '4': minutes_word = 'минуты';
        break;
        default: minutes_word = 'минут';
        break;
      }
      switch (hours_string[hours_string.length-1]) {
        case '1': hours_word = 'час';
        break;
        case '2': minutes_word = 'часа';
        break;
        case '3': minutes_word = 'часа';
        break;
        case '4': minutes_word = 'часа';
        break;
        default: hours_word = 'часов';
        break;
      }
      return `${formatted.hours()+24*formatted.days()} ${hours_word} ${formatted.minutes()} ${minutes_word}`;
    }
  },
  created() {
    this.fetchData();
    this.fetching = setInterval(() => this.fetchData(), 10000);
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
    videoSrc: {
      get: function() {
        return this.getCurrentStream().video_link;
      }
    }
  }
};
</script>

<style lang="scss" scoped>
.iframe-wrapper {
  height: 100%;
  background-size: cover;
}
</style>