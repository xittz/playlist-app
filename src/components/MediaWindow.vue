<template>
    <div class="content">
        <img v-if="file.mediaType == 'image'" :src="file.url">
        <video v-if="file.mediaType == 'video'" :src="file.url" controls loop autoplay>
            Your browser doesn't support HTML5 media
        </video>
    </div>
</template>

<script>
export default {
  name: 'MediaWindow',
  props: {
    file: Object
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
