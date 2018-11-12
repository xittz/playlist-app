<template>
  <div id="app">
    <div class="layout">
      
      <!-- LEFT BAR -->
      <div class="nav-control has-background-grey">
        <div class="playlist column is-narrow">
          <!-- CONTROLS -->
          <div class="buttons are-medium">
            <button class="button is-info" @click="play()">Play</button>
            <button class="button is-info" @click="reset()">Reset</button>
          </div>
          <!-- FILE DROPBOX -->
          <div class="dropbox">
            <input type="file" multiple @change="uploadFiles($event.target.files)" class="input-file">
              <p>
                Drag your file(s) here
              </p>
          </div>
          <!-- DRAGGABLE LIST OF FILES -->
          <draggable :list="files" class="dragArea">
            <PlaylistItem 
              v-for="(file, index) in files"
              :key="index"
              :file="file"
              @deleteFile="deleteFile($event)"/>
          </draggable>
        </div>
      </div>

      <!-- RIGHT BAR -->
      <div class="main">
        <MediaWindow v-if="isRunning" :file="currentFile"/>
      </div>
    </div>

  </div>
</template>

<script>
import PlaylistItem from './components/PlaylistItem.vue'
import MediaWindow from './components/MediaWindow.vue'
import draggable from 'vuedraggable'

export default {
  name: 'app',
  data() {
    return {
      files: [], // file = {id, name, url, mediaType, showDuration},
      isRunning: false,
      currentFile: null,
      timer: null,
      time: 0
    }
  },
  components: {
    PlaylistItem,
    MediaWindow,
    draggable
  },
  methods: {
    // invoked by PlaylistItem component, deletes a file from the list
    // if it's the current one - calls "playNextMedia"
    deleteFile(file) {
      if (this.files.length == 1)
        this.reset();
      else {
        if (this.currentFile.id == file.id)
          this.playNextMedia();
        this.files.splice(this.files.indexOf(file), 1)
      }
    },
    // invoked by file uploader (input), parses uploaded files and adds them to the list
    uploadFiles(files) {
      Array
        .from(Array(files.length).keys())
        .map(x => {
          let mediaType = files[x].type.split('/')[0]
          if (mediaType == 'image' || mediaType == 'video')
            this.files.push({
              id: '_' + Math.random().toString(36).substr(2, 9), // generate random id
              name: files[x].name,
              url: URL.createObjectURL(files[x]),
              mediaType: mediaType,
              showDuration: 5
            })
        })
    },
    // invoked by "play" button, turns on the showreel
    play() {
      this.currentFile = this.files[0];
      this.time = parseInt(this.files[0].showDuration);
      this.playMedia();
    },
    // an iteration of the showreel - shows current file
    // for the specified time
    playMedia() {
      this.isRunning = true;
      this.timer = setInterval(() => {
        if (this.time > 0) {
          this.time -= 1;
        } else {
          this.playNextMedia();
        }
      }, 1000)
    },
    // changes current file to the next one and calls the playMedia
    playNextMedia() {
      clearInterval(this.timer);
      let currentIdx = this.files.indexOf(this.currentFile);
      let newIdx = (currentIdx + 1) % this.files.length;
      this.currentFile = this.files[newIdx]
      this.time = parseInt(this.files[newIdx].showDuration)
      this.playMedia();
    },
    // "hard" reset, changes state to default settings
    reset() {
      this.isRunning = false;
      this.currentFile = null;
      clearInterval(this.timer)
      this.time = 0;
      this.files = []
    },
  }
}
</script>

<style>

html,
body {
  height: 100%;
  padding: 0;
  margin: 0;
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

.layout {
  min-height: 100vh;
  display: -ms-flexbox;
  display: -webkit-box;
  display: -moz-box;
  display: -ms-box;
  display: box;
  
  -ms-flex-direction: row;
  -webkit-box-orient: horizontal;
  -moz-box-orient: horizontal;
  -ms-box-orient: horizontal;
  box-orient: horizontal;
}

.nav-control {
  width: 300px;
  -ms-flex: 0 100px;
  -webkit-box-flex:  0;
  -moz-box-flex:  0;
  -ms-box-flex:  0;
  box-flex:  0;  
}

.main {
  background: aliceblue;
 -ms-flex: 1;
 -webkit-box-flex: 1;
 -moz-box-flex: 1;
 -ms-box-flex: 1;
 box-flex: 1;  
}

.buttons {
  padding: 20px;
  margin: 0 auto;
}

.dropbox {
  outline: 2px dashed grey; /* the dash box */
  outline-offset: -10px;
  background: #eee;
  color: dimgray;
  padding: 10px 10px;
  height: 120px; /* minimum height */
  position: relative;
  cursor: pointer;
  margin-bottom: 40px;
}
  
.input-file {
  opacity: 0; /* invisible but it's there! */
  width: 100%;
  height: 100px;
  cursor: pointer;
  position: absolute;
	left: 0;
}
  
.dropbox:hover {
  background: #ddd; /* when mouse over to the drop zone, change color */
}
  
.dropbox p {
  font-size: 1.2em;
  text-align: center;
  line-height: 100px;
}
</style>
