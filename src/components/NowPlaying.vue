<template>
  <div id="nowPlayingTab">
    <transition name="fade" mode="out-in">
      <div :key="properties.nowPlaying.song" class="currentlyPlaying">
        <div>
          <img :src="properties.nowPlaying.artwork_small" class="currentSongImg" />
        </div>

        <div class="songInfo">
          <p class="artist">{{ properties.nowPlaying.artist }}</p>
          <p>{{ properties.nowPlaying.song }}</p>
        </div>
      </div>
    </transition>

    <h2>Coming Up</h2>

    <div id="comingUpContainer">
      <div
        v-for="(item, itemIndex) in properties.queue"
        :key="'item' + itemIndex"
        class="upNext"
      >
        <div class="songInfo">
          <p class="artist">{{ item.artist }}</p>
          <p>{{ item.song }}</p>
        </div>

        <div class="songPopularityWrapper">
          <div v-if="item.processing" class="popularityVoteContainer">
            <Spinner />
          </div>

          <div class="popularityVoteContainer" v-else-if="!item.userVoted">
            <div
              class="thumbWrapper"
              @click="handleSongVoting(itemIndex, item.pick_id, true)"
            >
              <img src="@/assets/thumb.svg" />
            </div>

            <div
              class="thumbWrapper dislikeThumbs"
              @click="handleSongVoting(itemIndex, item.pick_id, false)"
            >
              <img src="@/assets/thumb.svg" />
            </div>
          </div>

          <div class="songVotes">{{ item.likes - item.dislikes }}</div>
        </div>

      </div>
    </div>
  </div>
</template>

<script>
import Spinner from './Spinner.vue';

export default {
  name: 'NowPlaying',
  components: {
    Spinner,
  },
  props: {
    properties: Object
  },
  data() {
    return {
      nowPlaying: {},
      queue: []
    };
  },
  computed: {

  },
  methods: {
    /**
     * Emits object containing vote data
     * @param {Number} itemIndex - index of song in queue
     * @param {Number} pickId - pick_id of song
     * @param {Boolean} isVoteUp - user's vote option for song; true: like, false: dislike
     */
    handleSongVoting(itemIndex, pickId, isVoteUp) {
      this.$emit('handle-song-voting', {itemIndex, pickId, isVoteUp});
    },
  }
};
</script>

<style scoped>
#nowPlayingTab {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
}

.currentlyPlaying {
  height: 120px;
  display: flex;
  align-items: center;
}

.currentSongImg {
  width: 5.5rem;
  padding: 12px;
  border-radius: 24px;
}

.songInfo {
  text-align: left;
}

.upNext {
  padding: 8px 16px;
  display: flex;
  justify-content: space-between;
  border-bottom: 1px solid #eee;
}

.artist {
  font-weight: bold;
  margin-bottom: 4px;
}

.songPopularityWrapper {
  display: flex;
}

.popularityVoteContainer {
  width: 60px;
  display: flex;
  align-items: center;
  justify-content: center;
}

#comingUpContainer {
  overflow-y: auto;
}

.thumbWrapper {
  width: 15px;
  display: flex;
  padding: 0 8px;
}

.thumbWrapper:hover {
  cursor: pointer;
  transition: 0.35s ease;
}

.dislikeThumbs {
  transform: rotate(180deg);
}

.songVotes {
  width: 12px;
  display: flex;
  align-items: center;
  justify-content: flex-end;
  padding: 0px 8px;
}

/** FADE ANIMATION */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.2s ease-in;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>