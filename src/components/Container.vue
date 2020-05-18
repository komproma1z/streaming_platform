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
        :volume="volume"
        :fetchData="fetchData"
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
  },
  created() {
    this.fetchData();
  },
  computed: {
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