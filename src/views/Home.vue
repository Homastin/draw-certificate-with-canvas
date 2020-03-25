<template>
  <div class="home">
    <canvas-certificate
      :canvasId="canvasId"
      :imageSrc="imageSrc"
    ></canvas-certificate>
  </div>
</template>

<script>

import CanvasCertificate from '@/components/CanvasCertificate';

export default {
  name: 'Home',
  components: {
    CanvasCertificate,
  },
  data() {
    return {
      canvasId: 'eleCert',
      imageSrc: require('@/components/CanvasCertificate/bg3.jpg'),
    }
  },
};
</script>
<style lang="scss">
  .home {
   width: 50%;
  }
</style>

