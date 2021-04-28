<template>
  <div id="queued">

    <transition name="fade" mode="out-in">
      <div :key="nowPlaying.song" class="currentlyPlaying">
        <div>
          <img :src="nowPlaying.artwork_small" class="currentSongImg" />
        </div>

        <div class="songInfo">
          <p class="artist">{{ nowPlaying.artist }}</p>
          <p>{{ nowPlaying.song }}</p>
        </div>
      </div>
    </transition>

    <p class="sectionLabel">Coming Up</p>

    <div id="comingUpContainer">
      <div
        v-for="(item, itemIndex) in queue"
        :key="'item' + itemIndex"
        class="upNext"
      >
        <div class="songInfo">
          <p class="artist">{{ item.artist }}</p>
          <p>{{ item.song }}</p>
        </div>

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

        <div v-else class="songVotes">{{ item.likes - item.dislikes }}</div>
      </div>
    </div>
  </div>
</template>

<script>
import Spinner from "./Spinner.vue";

export default {
  name: "Queued",
  components: {
    Spinner,
  },
  data() {
    return {
      nowPlaying: {},
      queue: []
    };
  },
  methods: {
    /**
     * Makes api call to grab queue data
     */
    getQueueData() {
      fetch("https://api.rockbot.com/v3/engage/now_playing?queue=1", {
        method: "GET",
        withCredentials: true,
        headers: {
          Authorization: "2ab742c917f872aa88644bc8f995e03159b2",
          "Content-Type": "application/json",
        },
      })
        .then((response) => response.json())
        .then((data) => {
            this.nowPlaying = data.response.now_playing
            let songQueue = data.response.queue;

            songQueue.map((song) => {
                song.processing = false;
                song.userVoted = false;
            });

            this.queue = songQueue;
        })
        .catch((err) => {
          console.log("there is an error: ", err);
        });
    },
    /**
     * Makes api call to update song popularity based on user's vote
     * @param {Number} pickId - pick_id of song
     * @param {Boolean} isVoteUp - user's vote option for song; true: like, false: dislike
     */
    handleSongVoting(itemIndex, pickId, isVoteUp) {
      // should a new key be added instead? How to keep track of what songs have been voted on?
      this.queue[itemIndex].processing = true;

      let url = isVoteUp
        ? "https://api.rockbot.com/v3/engage/vote_up"
        : "https://api.rockbot.com/v3/engage/vote_down";

      fetch(`${url}?pick_id=${pickId}`, {
        method: "POST",
        withCredentials: true,
        headers: {
          Authorization: "2ab742c917f872aa88644bc8f995e03159b2",
          "Content-Type": "application/json",
        },
      })
        .then((response) => response.json())
        .then((data) => {
          console.log("here it is", data);
          this.queue[itemIndex].processing = false;
          this.queue[itemIndex].userVoted = true;
          this.queue = data.response.queue;
        })
        .catch((err) => {
          console.log("there is an error: ", err);
        });
    },
  },
  mounted() {
    this.getQueueData();

    setInterval(() => {
      this.getQueueData();
    }, 30000);
  },
};
</script>

<style scoped>
#queued {
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
  display: flex;
  align-items: center;
  justify-content: flex-end;
  padding-right: 10px;
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