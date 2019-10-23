<template>
  <div class="home">
    <h1>sender</h1>
    <div class='video-box'>
      <div>
        <span>local</span>
        <video id="local" autoplay></video>
      </div>
      <div>
        <span>remote</span>
        <video id="remote" autoplay></video>
      </div>
    </div>
    <button @click="newPeer">call</button>
  </div>
</template>

<script>
// @ is an alias to /src
/* eslint-disable */
import Peer from 'peerjs';
import { desktopCapturer } from 'electron'

export default {
  name: 'home',
  data () {
    return {
      peer: null
    }
  },
  components: {
  },
  mounted () {
    // this.newPeer()
  },
  methods: {
    newPeer () {
      this.peer = new Peer('sender', { host: 'localhost', port: 9000, path: '/' })
      // const conn = peer.connect('receiver')
      // conn.on('open', () => {
      //   conn.send('hi!')
      // })
      this.play()
    },
    play () {
      var that = this
      var yourVideo = document.getElementById('local')

      if (this.hasUserMedia()) {
        desktopCapturer.getSources({ types: ['window', 'screen'] }).then(async sources => {
          try {
            const stream = await navigator.mediaDevices.getUserMedia({
              audio: false,
              video: {
                mandatory: {
                  chromeMediaSource: 'desktop'
                }
              }
            })
            console.log('■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■')
            console.log('stream:', stream)
            console.log('■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■')
            yourVideo.srcObject = stream
            const call = that.peer.call('receiver', stream)
            console.log('■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■')
            console.log('call:', call)
            console.log('■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■')
            // call.on('stream', remoteStream => {
            //   document.querySelector('video#remote').srcObject = remoteStream
            // })
            // if (this.hasRTCPeerConnection()) {
            //   // 稍后我们实现 startPeerConnection
            //   this.startPeerConnection(stream)
            // } else {
            //   alert('没有RTCPeerConnection API')
            // }
          } catch (e) {
            this.handleError(e)
          }
        })
      } else {
        alert('没有userMedia API')
      }
    },
    hasUserMedia () {
      navigator.getUserMedia = navigator.getUserMedia || navigator.msGetUserMedia || navigator.webkitGetUserMedia || navigator.mozGetUserMedia
      return !!navigator.getUserMedia
    },
    hasRTCPeerConnection () {
      window.RTCPeerConnection = window.RTCPeerConnection || window.webkitRTCPeerConnection || window.mozRTCPeerConnection || window.msRTCPeerConnection
      return !!window.RTCPeerConnection
    },
    handleError (err) {
      console.log('■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■')
      console.log('err:', err)
      console.log('■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■')
    },

  }
}
</script>

<style scoped>
.video-box {
  display: flex;
}
.video-box div {
  display: flex;
  flex-direction: column;
}
#local {
  width: 500px;
  border: 1px solid #108ee9;
}
#remote {
  width: 500px;
  border:1px solid #108ee9;
}
</style>
