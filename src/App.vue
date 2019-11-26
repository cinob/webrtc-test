<template>
  <div id="app">
    <div class="camera">
      <video id="video">Video stream not available.</video>
      <button id="startbutton">Take photo</button>
    </div>
    <canvas id="canvas">
    </canvas>
    <div class="output">
      <img id="photo" alt="The screen capture will appear in this box.">
    </div>
  </div>
</template>

<script>

export default {
  name: 'app',
  // data: function () {
  //   return {
  //     width: 320,
  //     height: 0,
  //     streaming: false,

  //   }
  // }
  methods: {
    init () {
      this.width = 320
      this.height = 0
      this.streaming = false
      this.video = null
      this.canvas = null
      this.photo = null
      this.startbutton = null
    },
    startup () {
      this.video = document.getElementById('video')
      this.canvas = document.getElementById('canvas')
      this.photo = document.getElementById('photo')
      this.startbutton = document.getElementById('startbutton')

      navigator.mediaDevices.getUserMedia({
        video: true,
        audio: false
      }).then(function (stream) {
        this.video.srcObject = stream
        this.video.play()
      }).catch(function (err) {
        console.log('An error occurred: ' + err)
      })

      this.video.addEventListener('canplay', function (ev) {
        if (!this.streaming) {
          this.height = this.video.videoHeight / (this.video.videoWidth / this.width)
          // Firefox currently has a bug where the height can't be read from
          // the video, so we will make assumptions if this happens.
          if (isNaN(this.height)) {
            this.height = this.width / (4 / 3)
          }
          this.video.setAttribute('width', this.width)
          this.video.setAttribute('height', this.height)
          this.canvas.setAttribute('width', this.width)
          this.canvas.setAttribute('height', this.height)
          this.streaming = true
        }
      }, false)

      let _t = this
      this.startbutton.addEventListener('click', function (ev) {
        _t.takepicture()
        ev.preventDefault()
      }, false)
      this.clearphoto()
    },
    clearphoto () {
      let context = this.canvas.getContext('2d')
      context.fillStyle = '#AAA'
      context.fillRect(0, 0, this.canvas.width, this.canvas.height)
      let data = this.canvas.toDataURL('image/png')
      this.photo.setAttribute('src', data)
    },
    takepicture () {
      let context = this.canvas.getContext('2d')
      if (this.width && this.height) {
        this.canvas.width = this.width
        this.canvas.height = this.height
        context.drawImage(this.video, 0, 0, this.width, this.height)
        let data = this.canvas.toDataURL('image/png')
        this.photo.setAttribute('src', data)
      } else {
        this.clearphoto()
      }
    }
  },
  mounted () {
    this.init()
    window.addEventListener('load', this.startup, false)
  }
}
</script>

<style lang="stylus">
#video {
  border: 1px solid black;
  box-shadow: 2px 2px 3px black;
  width:320px;
  height:240px;
}

#photo {
  border: 1px solid black;
  box-shadow: 2px 2px 3px black;
  width:320px;
  height:240px;
}

#canvas {
  display:none;
}

.camera {
  width: 340px;
  display:inline-block;
}

.output {
  width: 340px;
  display:inline-block;
}

#startbutton {
  display:block;
  position:relative;
  margin-left:auto;
  margin-right:auto;
  bottom:32px;
  background-color: rgba(0, 150, 0, 0.5);
  border: 1px solid rgba(255, 255, 255, 0.7);
  box-shadow: 0px 0px 1px 2px rgba(0, 0, 0, 0.2);
  font-size: 14px;
  font-family: "Lucida Grande", "Arial", sans-serif;
  color: rgba(255, 255, 255, 1.0);
}

.contentarea {
  font-size: 16px;
  font-family: "Lucida Grande", "Arial", sans-serif;
  width: 760px;
}
</style>
