<script>
import MusicPlayer from './MusicPlayer.vue';
    export default {
        components: { MusicPlayer },
        props:['searchQuery', 'songs'],
        data() {
            return {
              currentSongIndex: null,
              currentSongFile: null,
              currentSongName: null,
              isPlaying: false,
              isLooped: false,
              errorMessage: '',
              audio: new Audio()
            }
        },
        methods: {
          playInitial() {
            if(this.currentSongFile === null) {
              this.errorMessage = 'No Audio Source Provided';
            }
            else {
              this.errorMessage = '';
              const progress = document.querySelector('#seek');
              this.audio.src = this.currentSongFile;
              progress.style.width = 0;
              this.isPlaying = true;
              this.audio.play();
            }
          },
          updateIsPlaying(state) {
            this.isPlaying = state;
          },
          nextSong() {
            if(this.currentSongIndex === null) this.currentSongIndex = 0
            else this.currentSongIndex += 1;
            if(this.currentSongIndex >= this.songs.length) this.currentSongIndex = 0;
            this.currentSongFile = this.songs[this.currentSongIndex].preview_url;
            this.currentSongName = this.songs[this.currentSongIndex].name;
            this.playInitial();
          },
          prevSong() {
            if(this.currentSongIndex === null) this.currentSongIndex = this.songs.length - 1;
            else this.currentSongIndex -= 1;
            if(this.currentSongIndex < 0) this.currentSongIndex = this.songs.length - 1;
            this.currentSongFile = this.songs[this.currentSongIndex].preview_url;
            this.currentSongName = this.songs[this.currentSongIndex].name;
            this.playInitial();
          },
          stopSong() {
            document.querySelector('#seek').style.width = '100%';
            this.currentSongIndex =  null;
            this.currentSongFile = null;
            this.currentSongName = null;
            this.isPlaying = false;
            this.audio.pause();
          },
          toggleRepeat() {
            this.isLooped = !this.isLooped;
            document.getElementById('repeatButton').classList.toggle('repeat-active');
          }
        },
    }
</script>

<template>
    <div class="text-red flex justify-center">{{ errorMessage }}</div>
    <div class="songs my-10" v-show="songs.length > 0" id="songs">
    <ul>
      <li class="pb-5 pt-5 pl-5 song flex align-center" v-for="song, index in songs" :key="song.id" @click="()=> {
        if(song.preview_url !== null) {
          this.currentSongIndex = index;
          this.currentSongFile = song.preview_url;
          this.currentSongName = song.name;
          playInitial();
        }
        else {
          this.errorMessage = 'No Audio Source Provided';
          stopSong();
        }
      }">
        <img :src="song.album.images[2].url" class="mr-6"/>
        <div class="inline-block">
          <h4 class="font-medium">{{ song.name }}</h4>
          <h6 v-for="singer in song.artists" :key="singer.id">{{ singer.name }}</h6>
        </div>
        <hr>
      </li>
    </ul>
  </div>
  <MusicPlayer 
  :songFile="currentSongFile" 
  :songIndex="currentSongIndex"
  :songName = "currentSongName"
  :playing="isPlaying" 
  :audio="audio" 
  :isRepeat="isLooped"
  @trigger-play="updateIsPlaying($event)" 
  @previousSong="prevSong()" 
  @nextSong="nextSong()"
  @stopSong="stopSong()"
  @toggleRepeat="toggleRepeat()"
  />
</template>