<template>
  <div id="tabs">
    <div class="tabMainContent">
      <keep-alive>
        <component
          :is="currentComponent"
          v-bind="compProps"
          @handle-song-voting="updateSongVote"
          @handle-queue-update="getQueueData"
        />
      </keep-alive>
    </div>

    <div class="tabMenu">
      <div
        v-for="(tab, tabIndex) in tabs"
        :key="'tab' + tabIndex"
        class="tabLabels"
        :class="{ activeTab: tab.component === currentComponent }"
        @click="handleSwitchTab(tabIndex)"
      >
        <p>{{ tab.name }}</p>
      </div>
    </div>
  </div>
</template>

<script>
import NowPlaying from './NowPlaying.vue';
import Request from './Request.vue';

export default {
  name: 'Tabs',
  components: {
    NowPlaying,
    Request,
  },
  data() {
    return {
      tabs: [
        { name: 'Now Playing', component: 'NowPlaying' },
        { name: 'Request', component: 'Request' }
      ],
      currentComponent: 'NowPlaying',

      nowPlaying: {},
      queue: [],
    };
  },
  computed: {
    compProps() {
      let properties;

      if (this.currentComponent === 'NowPlaying') {
        properties = {
          nowPlaying: this.nowPlaying,
          queue: this.queue,
        };
      }

      return { properties };
    },
  },
  methods: {
    /**
     * Updates current displayed tab on user click
     * @param {Number} tabIndex - index of tab clicked
     */
    handleSwitchTab(tabIndex) {
      this.currentComponent = this.tabs[tabIndex].component;
    },
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
          this.nowPlaying = data.response.now_playing;
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
     * @param {{itemIndex: Number, pickId: Number, isVoteUp: Boolean}} voteInfo - 
     * index of song in queue
     * pick_id of song
     * user's vote option for song; true: like, false: dislike
     */
    updateSongVote(voteInfo) {
      let {itemIndex, pickId, isVoteUp} = voteInfo;
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
          this.queue[itemIndex].processing = false;
          this.queue[itemIndex].userVoted = true;
          this.queue = data.response.queue;
        })
        .catch((err) => {
          console.log("there is an error: ", err);
        });
    }
  },
  mounted() {
    this.getQueueData();

    // Update data every 30 seconds
    // setInterval(()=> {
    //   this.getQueueData();
    // }, 30000)
  },
};
</script>

<style>
#tabs {
  display: flex;
  width: 100%;
  height: 100%;
  flex-direction: column;
}

.tabMainContent {
  height: calc(100% - 50px);
  display: flex;
  overflow: hidden;
}

.tabMenu {
  height: 50px;
  display: flex;
}

.tabLabels {
  height: 50px;
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #666869;
}

.tabLabels:hover {
  background: #409eeb;
  color: white;
  transition: 0.35s ease;
}

.activeTab {
  background: #007ee4;
  color: white;
}
</style>