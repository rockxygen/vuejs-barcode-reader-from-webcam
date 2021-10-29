<template>
  <div id="app">
    <video id="camera" ref="camera" autoplay muted hidden />
    <canvas id="canvas" ref="canvas" width="500" height="300" />
    <hr>
    {{ barcodes }}
  </div>
</template>

<script>
export default {
  name: 'App',
  data(){
    return {
      canvas: null,
      video: null,
      barcodes: [],
      detector: null,
      dataUrl: null
    }
  },
  methods: {
    openCam(){
      navigator.mediaDevices.getUserMedia({
        video: {
          width: 500,
          height: 300,
          facingMode: 'environment'
        }
      }).then(stream => {
        this.video.srcObject = stream;
        this.video.play();

        const barcode = new window.BarcodeDetector({ formats: ['qr_code', 'ean_13', 'ean_8'] });

        setInterval(() => {
          this.canvas.getContext('2d').drawImage(this.video, 0, 0, 500, 300);
          barcode.detect(this.canvas)
            .then(([data]) => {
              if (data) {
                this.barcodes.push(data.rawValue);
                console.log(data);
              }
            })
            .catch(err => console.log(err))
        }, 100);

      }).catch(err => alert(err));
    }
  },
  mounted(){
    const c = document.getElementById('canvas');
    const v = document.getElementById('camera');
    this.dataUrl = c.toDataURL();
    this.video = v;
    this.canvas = c;
    this.openCam();
  }
}
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
