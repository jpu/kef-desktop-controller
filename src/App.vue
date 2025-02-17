<template>
    <div id="app" class="py-5 mx-10">
        <div v-show="view === 'playback'">
            <input-source-controls
                :controller="controller"
                v-model="inputSource"
                @show-settings="view = 'settings'"
                @show-dsp="view = 'dsp'"
            />
            <volume-controls ref="volume" :controller="controller" />
            <playback-controls :controller="controller" :inputSource="inputSource" />
        </div>
        <div v-show="view === 'dsp'">
            <navigation-row @back="view = 'playback'" />
            <sub-low-pass-controls ref="lowPass" :controller="controller"  />
            <sub-controls ref="sub" :controller="controller"  />
        </div>
        <div v-show="view === 'settings'">
            <navigation-row @back="view = 'playback'" />
            <ip-input :controller="controller" />
        </div>
    </div>
</template>

<script lang="ts">
import Vue from "vue";
import { InputSource } from "./enums";
import VolumeControls from "./components/VolumeControls.vue";
import SubControls from "./components/DSP/SubControls.vue";
import NavigationRow from "./components/DSP/NavigationRow.vue";
import PlaybackControls from "./components/PlaybackControls.vue";
import InputSourceControls from "./components/InputSourceControls.vue";
import SubLowPassControls from "@/components/DSP/SubLowPassControls.vue";
import IpInput from "@/components/Settings/IpInput.vue";
import KefController from "./utils/kefControl";
// "192.168.1.218"

export default Vue.extend({
    components: {
        VolumeControls,
        SubControls,
        PlaybackControls,
        InputSourceControls,
        NavigationRow,
        SubLowPassControls,
        IpInput
    },
    watch: {
        view: function(val) {
            this.refreshData();
        }
    },
    data() {
        return {
            controller: {} as KefController,
            inputSource: InputSource.NotAvailable as InputSource,
            view: "playback" as "playback"|"dsp"|"settings",
            refreshInterval: null! as any
        };
    },
    async beforeDestroy() {
        clearInterval(this.refreshInterval);
        this.refreshInterval = null;
        await this.controller.closeConnection();
    },
    async mounted() {
        document.body.setAttribute("vs-theme", "dark");
        setTimeout(async () => {
            this.controller = new KefController();
            this.fetchVolumeAndSourceData();
            this.refreshInterval = setInterval(this.fetchVolumeAndSourceData, 6000);
        }, 200);
    },
  
    methods: {
        async refreshData() {
            if (this.view === "dsp") this.fetchDspData();
            else if (this.view === "playback") this.fetchVolumeAndSourceData();            
        },
        async setMode() {
            await this.controller.setSource(this.inputSource);
        },
        async turnOff() {
            await this.controller.turnOff();
            this.inputSource = InputSource.Off;
        },
        async fetchVolumeAndSourceData() {
            setTimeout(async () => {
                this.inputSource = await this.controller.getSource();
                (this.$refs.volume as HTMLFormElement).getVolume();
            }, 200);
        },
        async fetchDspData() {
            setTimeout(async () => {
                await(this.$refs.sub as HTMLFormElement).getSubDb();
                await(this.$refs.lowPass as HTMLFormElement).getSubHz();
            }, 200);
        }
    }
});
</script>