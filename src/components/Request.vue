<template>
    <div id="requestTab">
        <h2>Top Artists</h2>

        <div class="topThree">
            <div
                v-for="(item, itemIndex) in response.slice(0, 3)" 
                :key="'item' + itemIndex"
                class="topThreeArtists"
            >
                <div class="artistArtworkWrapper" @click="addArtistToQueue(itemIndex, item.artist_id)">
                    <img :src="item.artwork_small" class="artwork" />
                </div>

                <div class="artistName">
                    <p>{{ itemIndex + 1 }}. {{ item.artist }}</p>
                    <Spinner v-if="item.processing" class="spinner" />
                    <span v-else-if="item.addedToQueue" class="checkmark">&#10003;</span>
                </div>
            </div>
        </div>

        <div id="topArtistsList">
            <div 
                v-for="(item, itemIndex) in response.slice(3)" 
                :key="'item' + itemIndex"
                class="artistItem"
            >
                <div>
                    <p>{{ itemIndex + 4 }}. {{ item.artist }}</p>
                </div>

                <div class="addIcon" @click="addArtistToQueue(itemIndex + 3, item.artist_id)">
                    <Spinner v-if="item.processing" class="spinner" />
                    <span v-else-if="!item.addedToQueue">+</span>
                    <span v-else class="checkmark">&#10003;</span>     
                </div>

            </div>
        </div>
    </div>
</template>

<script>
import Spinner from './Spinner.vue';

export default {
    name: 'Request',
    components: {
        Spinner
    },
    data() {
        return {
            response: []
        }
    },
    methods: {
        /**
         * Adds requested artist to queue.
         * @param {Number} artistIndex - index of selected artist in array
         * @param {Number} artistId - selected artist_id
         */
        addArtistToQueue(artistIndex, artistId) {
            this.response[artistIndex].processing = true;

            fetch(`https://api.rockbot.com/v3/engage/request_artist?artist_id=${artistId}`, {
                method: "POST",
                withCredentials: true,
                headers: {
                    Authorization: "2ab742c917f872aa88644bc8f995e03159b2",
                    "Content-Type": "application/json",
                }
            })
            .then((response) => response.json())
            .then(() => {
                this.response[artistIndex].processing = false;
                this.response[artistIndex].addedToQueue = true;

                this.$emit('handle-queue-update');
            })
            .catch(err => {
                console.log('there is an error: ', err)
            })
        }
    },
    mounted() {
        fetch("https://api.rockbot.com/v3/engage/top_artists", {
            method: "GET",
            withCredentials: true,
            headers: {
                Authorization: "2ab742c917f872aa88644bc8f995e03159b2",
                "Content-Type": "application/json",
                },
        })
        .then((response) => response.json())
        .then((data) => {
            let response = data.response;

            response.map(artist => {
                artist.processing = false;
                artist.addedToQueue = false;
            })

            this.response = response;
        })
        .catch(err => {
            console.log('there is an error: ', err)
        })
    }
}
</script>

<style scoped>
#requestTab {
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
}

.topThree {
    display: flex;
    justify-content: center;
    padding: 8px;
}

.topThreeArtists {
    padding: 0px 12px;
}

.artistArtworkWrapper {
    display: flex;
    justify-content: center;
    padding: 4px;
}

.artwork {
    border-radius: 50%;
    width: 5.5rem;
}

.artistName {
    display: flex;
}

.artistName > span {
    padding-left: 2px;
}

#topArtistsList {
    overflow-y: auto;
}

.artistItem {
    display: flex;
    justify-content: space-between;
    border-bottom: 1px solid #EEE;
    padding: 8px 16px;
}

.artistItem:nth-child(1) {
    border-top: 1px solid #EEE;
}

.addIcon {
    border: 1px solid #EEE;
    border-radius: 50%;
    width: 20px;
    height: 20px;
    display: flex;
    justify-content: center;
}

.addIcon:hover {
    transition: .35s ease;
    color: #007EE4;
    border: 1px solid #007EE4;
    cursor: pointer;
}

.checkmark {
    color: #007EE4;
}
</style>