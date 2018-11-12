<template>
  <div class="message is-info">
    <div class="message-header">
      <input class="input" type="number" min="1" v-model="file.showDuration">
      <span>{{ fileName }}</span><br>
      <button class="delete" @click="deleteFile()"></button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'PlaylistItem',
  props: {
    file: Object
  },
  methods: {
    // invoked by click on the "delete" button,
    // removes file from the playlist
    deleteFile() {
      this.$emit('deleteFile', this.file)
    }
  },
  computed: {
    // shortens the file name in case if it's too long
    fileName() {
      if (this.file.name.length < 18)
        return this.file.name;
      else
        return this.file.name.substring(0, 17) + '..'
    }
  },
  beforeDestroy() {
    delete this.file;
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

input {
    width: 50px;
}

</style>
