<template>
  <div class="related-list">
    <div class="song" v-for="video in relatedSongs" @click="chooseVideo(video)">
      <img :src="video.snippet.thumbnails.medium.url" />
      <p v-text="video.snippet.title" />
    </div>
  </div>
</template>

<script>
import PlaylistEventBus from '@/components/UserPages/Playlist/PlaylistEventBus.js'

export default {
  name: 'related-songs-list',
  props: ['currentSong'],
  data () {
    return {
      relatedSongs: null
    }
  },
  watch: {
    currentSong: function (obj) {
      if (!obj.vidId) {
        this.getRelatedVideos('d-diB65scQU') // Dont worry be happy
      } else {
        this.getRelatedVideos(obj.vidId)
      }
    }
  },
  methods: {
    chooseVideo (video) {
      this.$emit('changeSong', {
        song: {
          vidId: video.id.videoId
        }
      })
      PlaylistEventBus.$emit('changeSongUrl', 'https://www.youtube.com/watch?v=' + video.id.videoId)
    },
    getRelatedVideos (vidId) {
      this.axios.get('https://www.googleapis.com/youtube/v3/search',
        {
          params: {
            'relatedToVideoId': vidId,
            'part': 'snippet',
            'type': 'video',
            'maxResults': '3',
            'key': 'AIzaSyAtwxH_RK0NDVhNYLcTERQ9tTvAkBc01cQ'
          }
        }).then(res => {
          this.relatedSongs = res.data.items
        })
    }
  }
}
</script>

<style>
  .related-list {
    width: 400px;
    overflow: auto;
  }

  .related-list .song {
    cursor: pointer;
    display: flex;
    margin-top: 22px;
  }

  .related-list .song img {
    width: 130px;
    height: 90px;
    margin-right: 5px;
  }
</style>