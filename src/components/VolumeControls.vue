<template>
    <div class="mt-5">
        <h5 class="text-white text-md text-center font-semibold">Volume</h5>
        <vue-slider
            class="mt-10"
            :max="100"
            :min="0"
            v-model="volume"
            @drag-end="setVolume"
            tooltip="always"
            height="8px"
            :processStyle="{ backgroundColor: '#2074FF' }"
            :tooltipStyle="{ backgroundColor: '#2074FF' }"
            :drag-on-click="true"
        />
    </div>
</template>

<script lang="ts">
import Vue from "vue";
import VueSlider from "vue-slider-component";
import KefController from "../utils/kefControl";
import "vue-slider-component/theme/default.css";

export default Vue.extend({
    components: { VueSlider },
    props: {
        controller: {
            required: true,
            type: Object as () => KefController || null
        }
    },
    data() {
        return { volume: "Syncing ..." };
    },
    methods: {
        async getVolume() {
            if (this.controller){
                const volume = await this.controller.getVolume();
                if (volume > -1) this.volume = volume;
            }
        },
        async setVolume() {
            await this.controller.setVolume(this.volume);
        }
    }
});
</script>