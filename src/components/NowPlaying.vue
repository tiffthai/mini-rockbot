<template>
    <div id="queued">
        <div 
            v-for="(item, itemIndex) in nowPlaying" 
            :key="'item' + itemIndex"
            class="currentlyPlaying"
        >
            <div>
                <img :src="item.artwork_small" class="currentSongImg" />
            </div>
            
            <div class="songInfo">
                <p class="artist">{{ item.artist }}</p>
                <p>{{ item.song }}</p>
            </div>
        </div>

        <p class="sectionLabel">Coming Up</p>

        <div id="comingUpContainer">
            <div 
                v-for="(item, itemIndex) in rawQueue" 
                :key="'item' + itemIndex"
                class="upNext"
            >
                <div class="songInfo">
                    <p class="artist">{{ item.artist }}</p>
                    <p>{{ item.song }}</p>
                </div>

                <div class="songOptions" v-if="!userVoted[itemIndex]">
                    <div class="thumbWrapper" @click="handleSongVoting(itemIndex, item.pick_id, true)">
                        <img src="@/assets/thumb.svg" />
                    </div>
                    <div class="thumbWrapper dislikeThumbs" @click="handleSongVoting(itemIndex, item.pick_id, false)">
                        <img src="@/assets/thumb.svg" />
                    </div>
                </div>

                <div v-else class="songVotes">{{ item.likes - item.dislikes }}</div>
            </div>
        </div>

    </div>
</template>

<script>
export default {
    name: 'Queued',
    data() {
        return {
            nowPlaying: [],
            rawQueue: [],
            userVoted: []
        }
    },
    methods: {
        /**
         * Updates 
         * @param {Number} pickId - pick_id of song
         * @param {String} isVoteUp - user's vote option for song
         */
        handleSongVoting(itemIndex, pickId, isVoteUp) {
            // should a new key be added instead? How to keep track of what songs have been voted on?
            this.userVoted[itemIndex] = true; 

            let url = (isVoteUp) ? 
                    'https://api.rockbot.com/v3/engage/vote_up': 
                    'https://api.rockbot.com/v3/engage/vote_down';

            fetch(`${url}?pick_id=${pickId}`, {
                method: "POST",
                withCredentials: true,
                headers: {
                    Authorization: "2ab742c917f872aa88644bc8f995e03159b2",
                    "Content-Type": "application/json",
                }
            })
            .then((response) => response.json())
            .then((data) => {
                console.log("here it is", data)
            })
            .catch(err => {
                console.log('there is an error: ', err)
            })
        },

    },
    mounted() {
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
            console.log("here it is", data)
            this.nowPlaying.push(data.response.now_playing);
            this.rawQueue = data.response.queue;
            this.userVoted = new Array(this.rawQueue.length).fill(false);
        })
        .catch(err => {
            console.log('there is an error: ', err)
        })
    }
}
</script>

<style scoped>
#queued {
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
}

.currentlyPlaying {
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
    border-bottom: 1px solid #EEE;
}

.artist {
    font-weight: bold;
    margin-bottom: 4px;
}

.songOptions {
    display: flex;
    align-items: center;
}

#comingUpContainer {
    overflow-y: scroll;
}

.thumbWrapper {
    width: 15px;
    display: flex;
    padding: 0 8px;
}

.thumbWrapper:hover {
    cursor: pointer;
    transition: .35s ease;
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
</style>