<script>
    export default {
        props:['songFile', 'songIndex','songName', 'playing', 'audio', 'isRepeat'],
        data() {
            return {
            }
        },
        methods: {
            playAudio() {
                if(this.songIndex !== null) {
                    this.audio.play();
                    this.$emit("triggerPlay", true);
                }
            },
            pauseAudio() {
                if(this.songIndex !== null) {
                    this.audio.pause();
                    this.$emit("triggerPlay", false);
                }
            },
            updateSeeker() {
                const currentTime = this.audio.currentTime;
                const duration = this.audio.duration;
                const progressBar = document.querySelector('#seek');
                const progressPercent = (currentTime / duration) * 100;
                progressBar.style.width = `${progressPercent}%`;
                if(currentTime >= duration && this.isRepeat) {
                    this.playAudio();
                    this.$emit("triggerPlay", true);
                }
                else if(currentTime >= duration) {
                    this.pauseAudio();
                    this.$emit("triggerPlay", false);
                }
            },
            seekTo(e) {
                var x = e.offsetX;
                const progressBar = document.querySelector('.seek-total');
                var width = progressBar.offsetWidth;
                var percentage = x / width;
                var currentTime = percentage * this.audio.duration;
                this.audio.currentTime = currentTime;
            }
        },
        mounted() {
            if(this.songIndex !== null) {
                this.playAudio();   
            }
            this.audio.addEventListener('timeupdate', this.updateSeeker);
        }
    }
</script>

<template>
    <div class="player-div">
            <div class="flex justify-center">
                <h1 class="text-2xl font-bold" v-show="this.songName !== null">Now Playing: {{ this.songName }}</h1>
            </div>
            <div class="seek">
                
                <div class="seek-buttons">
                    <div><i class="bi bi-repeat-1 icon repeat-icon" id="repeatButton" @click="$emit('toggleRepeat')"></i></div>
                    <div>
                        <i class="bi bi-rewind-circle icon previous-icon" id="prevButton" @click="$emit('previousSong')"></i>
                    </div>
                    <div>
                        <i class="bi bi-pause-circle icon play-icon" v-show="this.playing" id="pauseButton"  @click="pauseAudio()"></i>
                        <i class="bi bi-play-circle icon play-icon" v-show="!this.playing" id="playButton" @click="playAudio()"></i>
                    </div>
                    <div>
                        <i class="bi bi-fast-forward-circle icon next-icon" id="nextButton" @click="$emit('nextSong')"></i>
                    </div>
                    <div>
                        <i class="bi bi-stop-circle stop-icon icon" id="stopButton" @click="$emit('stopSong')"></i>
                    </div>
                </div>
            <br/>
            <div class="seek-total" @click="seekTo">
                <div class="seek-current" id="seek"></div>
            </div>
        </div>
    </div>
</template>