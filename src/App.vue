<template>
  <div>
    <div class="flex h-screen w-screen">
      <div> <video ref="video" width="300" height="300"></video>
        <canvas ref="canvas" style="display: none"></canvas>
        <div v-if="qrCode">
          <p>QR Code: {{ qrCode }}</p>
        </div>
      </div>
      <div>
        asdkjfk
      </div>

    </div>
  </div>
</template>
<script>
import QrCode from "qrcode-reader";

export default {
  data() {
    return {
      qrCode: null,
    };
  },
  mounted() {
    this.initCamera();
  },
  methods: {
    initCamera() {
      const video = this.$refs.video;
      const canvas = this.$refs.canvas;
      const context = canvas.getContext("2d");
      const qr = new QrCode();

      navigator.mediaDevices
        .getUserMedia({
          video: { facingMode: "environment" },
        })
        .then((stream) => {
          video.srcObject = stream;
          video.play();

          requestAnimationFrame(tick);
        });

      function tick() {
        if (video.readyState === video.HAVE_ENOUGH_DATA) {
          canvas.width = video.videoWidth;
          canvas.height = video.videoHeight;
          context.drawImage(video, 0, 0, canvas.width, canvas.height);

          const imageData = context.getImageData(
            0,
            0,
            canvas.width,
            canvas.height
          );
          qr.callback = (error, result) => {
            if (error) {
              console.error(error);
            } else {
              this.qrCode = result.result;
            }
          };
          qr.decode(imageData);
        }

        requestAnimationFrame(tick);
      }
    },
  },
};
</script>