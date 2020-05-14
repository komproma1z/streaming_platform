<template>
  <div
    class="d-flex flex-column justify-end before-stream"
    :style="{backgroundImage:`url(${imageSrc})`}"
  >
    <div class="time-and-volume">
      <h2>До начала трансляции: {{ getTimeLeft(currentTime, this.getCurrentStream().started_at) }}</h2>
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
  props: ["getCurrentStream", "getTimeLeft", "volume", "imageSrc"],
  data() {
      return {
        currentTime: this.$moment().toISOString(),
      };
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
  background: linear-gradient(
    90deg,
    rgba(0, 0, 0, 1) 0%,
    rgba(0, 0, 0, 1) 26%,
    rgba(0, 0, 0, 0.1825105042016807) 71%
  );
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