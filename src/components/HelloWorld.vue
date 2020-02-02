<template>
  <div class="hello">
    <video
      id="caller"
      autoplay
      playsinline
    ></video>
    <video
      id="callee"
      autoplay
      playsinline
    ></video>
    <button @click="call"> CALL </button>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data: function () {
    return {
      localStream: null,
      remoteStream: null,
      servers: null,
      constraints: { video: true, audio: true },
      localPeerConnection: null,
      remotePeerConnection: null
    }
  },
  props: {
    msg: String
  },
  methods: {
    call () {
      this.localPeerConnection = new RTCPeerConnection(this.servers)
      console.log('local peer connection has been created')
      this.localPeerConnection.addEventListener('icecandidate', this.handleConnection)
      this.localPeerConnection.addEventListener(
        'iceconnectionstatechange',
        this.handleConnectionChange)

      this.remotePeerConnection = new RTCPeerConnection(this.servers)
      console.log('remote peer connection has been created')
      this.remotePeerConnection.addEventListener('icecandidate', this.handleConnection)
      this.remotePeerConnection.addEventListener(
        'iceconnectionstatechange',
        this.handleConnectionChange)

      this.remotePeerConnection.addEventListener('addstream', this.gotRemoteMediaStream)

      this.localPeerConnection.addStream(this.localStream)
      console.log('added local stream to localPeerConnection')

      console.log('localPeerConnection create offer start')
      this.localPeerConnection.createOffer({
        offerToReceiveVideo: 1
      }).then(this.createdOffer).catch()
    },
    handleConnection (event) {
      console.log(event)
      const peerConnection = event.target
      const iceCandidate = event.candidate
      if (iceCandidate) {
        const newIceCandidate = new RTCIceCandidate(iceCandidate)
        const otherPeer = this.getOtherPeer(peerConnection)
        console.log(otherPeer)
        debugger
        otherPeer.addIceCandidate(newIceCandidate)
          .then(() => {
            console.log('addIceCandidate success')
          })
          .catch((err) => {
            console.log(err)
            console.log('addIceCandidate does not success')
          })
      }
    },
    handleConnectionChange (event) {
      const peerConnection = event.target
      console.log('ICE state change event: ', peerConnection.iceConnectionState)
    },
    gotRemoteMediaStream (event) {
      const mediaStream = event.localStream
      this.remoteStream = mediaStream
    },
    createdOffer (description) {
      this.localPeerConnection.setLocalDescription(description)
        .then()
        .catch()

      this.remotePeerConnection.setRemoteDescription(description)
        .then()
        .catch()

      this.remotePeerConnection.createAnswer()
        .then(this.createdAnswer)
        .catch()
    },
    createdAnswer (description) {
      this.remotePeerConnection.setLocalDescription(description)
        .then()
        .catch()
    },
    getOtherPeer (peerConnection) {
      return (peerConnection === this.localPeerConnection) ? this.localPeerConnection : this.remotePeerConnection
    }
  },
  mounted () {
    navigator.mediaDevices.getUserMedia(this.constraints)
      .then(resp => {
        document.querySelector('#caller').srcObject = resp
        this.localStream = resp
        console.log('Received local stream')
      }).catch(err => console.log(err))
    this.remoteStream = document.querySelector('#callee').srcObject
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
