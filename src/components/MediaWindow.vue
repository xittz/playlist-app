<template>
    <div class="content">
        <img v-if="file.mediaType == 'image'" :src="file.url">
        <video v-if="file.mediaType == 'video'" :src="file.url" @ended="replay()" controls>
            Your browser doesn't support HTML5 media
        </video>
    </div>
</template>

<script>
export default {
  name: 'MediaWindow',
  props: {
    file: Object
  },
  data() {
    return {
        isPlaying: false,
    }
  },
  methods: {
    replay() {
        let video = document.querySelector('video')
        video.currentTime = 0;
        video.play();
    },
    playVideo() {
      if (this.file.mediaType === 'video') {
      	if (!this.isPlaying) {
          this.isPlaing = true;
          document.querySelector('video').play();
        }      
			}
		}
  },
  mounted() {
		this.playVideo();		
  },
  updated() {
		this.playVideo();
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

.content {
    display: flex;
    align-items: center;
    justify-content: center;
    height: 100vh;
}

video, img {
    max-width: 70%;
    max-height: 80%;
}

</style>
