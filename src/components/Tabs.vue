<template>
    <div id="tabs">
        <div class="tabMainContent">
            <keep-alive>  
                <component :is="currentComponent" />
            </keep-alive>
        </div>
 
        <div class="tabMenu">
            <div  
                v-for="(tab, tabIndex) in tabs" 
                :key="'tab' + tabIndex"
                class="tabLabels"
                :class="{'activeTab': tab.component === currentComponent}"
                @click="handleSwitchTab(tabIndex)"
            >
                <p>{{ tab.name }}</p>
            </div>
        </div>
    </div>
</template>

<script>
import NowPlaying from "./NowPlaying.vue";
import Request from "./Request.vue";

export default {
    name: 'Tabs',
    components: {
        NowPlaying,
        Request
    },
    data() {
        return {
            tabs: [
                {name: 'Now Playing', component: 'NowPlaying'},
                {name: 'Request', component: 'Request'}
            ],
            currentComponent: 'NowPlaying'
        }
    },
    methods: {
        /**
         * Updates current displayed tab on user click
         * @param {Number} tabIndex - index of tab clicked 
         */
        handleSwitchTab(tabIndex) {
            this.currentComponent = this.tabs[tabIndex].component;
            console.log(tabIndex)
        }
    },
    mounted() {

    }
}
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
</style>