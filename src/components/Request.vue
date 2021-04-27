<template>
    <div id="request">
        <div>
            <p class="sectionLabel">Top Artists</p>
        </div>

        <div class="top">
            <div
                v-for="(item, itemIndex) in topThree" 
                :key="'item' + itemIndex"
                class="topThreeItems"
            >
                <div>
                    <img :src="item.artwork_small" class="artwork" />
                </div>

                <div>
                    {{ itemIndex + 1 }}.
                    {{ item.artist }}
                </div>
            </div>
        </div>

        <div id="topArtistsList">
            <div 
                v-for="(item, itemIndex) in remaining" 
                :key="'item' + itemIndex"
                class="listItem"
            >
                <div>
                    {{ itemIndex + 4 }}.
                    {{ item.artist }}
                </div>

                <div class="addIcon" @click="addArtistToQueue(itemIndex + 4)">+</div>
            </div>

        </div>
    </div>
</template>

<script>
export default {
    name: 'Request',
    data() {
        return {
            response: []
        }
    },
    computed: {
        topThree() {
            let topThree = this.response.slice(0, 3);
            return topThree;
        },
        remaining() {
            let remaining = this.response.slice(3);
            return remaining;
        }
    },
    methods: {
        /**
         * Adds requested artist to queue.
         * @param {Number} itemIndex - index of artist selected
         */
        addArtistToQueue(itemIndex) {
            console.log(this.response[itemIndex])
            // console.log('check', this.response)
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
            console.log("top artists data:", data)
            this.response = data.response;
        })
        .catch(err => {
            console.log('there is an error: ', err)
        })
    }
}
</script>

<style scoped>
#request {
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
}

.top {
    display: flex;
    justify-content: center;
    padding: 8px;
}

.topThreeItems {
    padding: 0px 12px;
}

#topArtistsList {
    overflow-y: scroll;
}

.listItem {
    display: flex;
    justify-content: space-between;
    border-bottom: 1px solid #EEE;
    padding: 8px 16px;
}

.listItem:nth-child(1) {
    border-top: 1px solid #EEE;
}

.artwork {
    border-radius: 50%;
    width: 5.5rem;
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
</style>