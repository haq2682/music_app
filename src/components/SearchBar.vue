<script>
import axios from 'axios';
import SearchResults from './SearchResults.vue';
  export default {
    data() {
        return {
            search: '',
            songs: [],
            clientId: 'aff424b4f1e74e05942505bf9ffbce40',
            clientSecret: '2f83ec96234b4e268e47f499ae289daf',
            time_now: null,
            access_token_expire_time: null,
            access_token: null,
        };
    },
    methods: {
          searchSong(query) {
            document.getElementById("loader").classList.remove('hidden');
            document.getElementById("songs").classList.add('hidden');
            axios.get('https://api.spotify.com/v1/search?query='+ query + '&type=track&limit=10', {
              headers: {
                Authorization: 'Bearer ' + this.access_token
              }
            })
            .then((response) => {
              this.songs = response.data.tracks.items;
            })
            .catch((err) => {
              console.log(err);
            })
            .finally(()=>{
              document.getElementById("loader").classList.add('hidden');
              document.getElementById("songs").classList.remove('hidden');
            })
            
          },
          renewToken() {
            console.log('Renew Initiated');
            axios.post('https://accounts.spotify.com/api/token', `grant_type=client_credentials&client_id=${this.clientId}&client_secret=${this.clientSecret}`)
            .then((res)=> {
              console.log('Renew in process');
              localStorage.setItem("access_token", res.data.access_token);
              localStorage.setItem("access_token_expire_time", Date.now() + (res.data.expires_in * 1000));
              localStorage.setItem("time_now", Date.now());
              console.log('Token Renewed');
              location.reload();
            })
            .catch((err)=>console.log(err));
          }
        },
    beforeMount() {
      this.access_token = localStorage.getItem("access_token");
      this.access_token_expire_time = parseInt(localStorage.getItem("access_token_expire_time"));
      this.time_now = parseInt(localStorage.getItem("time_now"));
      if(this.access_token === null || Date.now() > this.access_token_expire_time) {
        this.renewToken();
      };
    },
    components: { SearchResults }
}
</script>

<template>
  <div class="searchbar">
    <div class="search">
      <v-text-field clearable variant="outlined" label="Search a Song" v-model="search" @keyup="searchSong(search)"></v-text-field> 
    </div>
  </div>
  <div class="flex justify-center hidden" id="loader"><img src="/loader.svg"/></div>
  <SearchResults :search-query="this.search" :songs="this.songs"/>
</template>
