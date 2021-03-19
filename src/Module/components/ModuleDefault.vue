<template>
  <v-container class="module-default pa-0">
    <div class="module-default__instructions">
      <v-expansion-panels v-model="showInstructions" class="module-default__instructions" flat>
        <v-expansion-panel>
          <v-expansion-panel-header
            v-show="showInstructions"
            hide-actions
            class="pa-0"
            @click="showInstructions = true"
          >
            <template v-slot="{ open }">
              <v-scroll-y-transition hide-on-leave>
                <div v-if="!open" class="d-flex flex-column justify-center">
                  <v-icon color="grey lighten-2" class="d-flex justify-center">
                    mdi-chevron-down
                  </v-icon>
                  <div color="grey lighten-2" class="module-default__collapse-title">
                    INSTRUCTIONS
                  </div>
                </div>
              </v-scroll-y-transition>
            </template>
          </v-expansion-panel-header>
          <v-expansion-panel-content>
            <Instruct readonly />
            <div @click="showInstructions = true">
              <div class="module-default__collapse-title">CLOSE</div>
              <!-- <div class="hr"/> OPTIONAL -->
              <v-icon color="grey lighten-2" class="d-flex justify-center"> mdi-chevron-up </v-icon>
            </div>
          </v-expansion-panel-content>
        </v-expansion-panel>
      </v-expansion-panels>
    </div>
    <v-progress-linear
      class="mt-2"
      color="#dedede"
      height="2"
      value="100"
      buffer-value="100"
      stream
    />

    <v-expansion-panels tile accordion flat class="module-default__playlist">
      <v-expansion-panel
        v-for="(linkObj, index) in trainData.videoLinks"
        :key="index"
        class="module-default__playlist-panel"
        :disabled="linkObj.disabled"
      >
        <v-expansion-panel-header
          :class="{ 'grey--text text--lighten-2': linkObj.disabled === true }"
          disable-icon-rotate
          class="module-default__video-title"
        >
          {{ linkObj.name }}
          <template v-slot:actions>
            <v-icon v-if="linkObj.finished == false && linkObj.disabled == false" color="warning">
              mdi-alert-circle
            </v-icon>
            <v-icon v-if="linkObj.finished" color="teal">mdi-check</v-icon>
          </template>
          <v-icon v-if="linkObj.disabled" style="position: absolute; right: 24px" color="error">
            mdi-lock-outline
          </v-icon>
        </v-expansion-panel-header>
        <v-expansion-panel-content class="module-default__video-content-panel">
          <iframe
            width="100%"
            height="380px"
            :src="`https://www.youtube.com/embed/${getYoutubeId(linkObj.link)}`"
            frameborder="0"
            allow="autoplay; encrypted-media"
            allowfullscreen
          ></iframe>
          <div class="d-flex justify-center">
            <v-checkbox
              v-model="trainData.videoLinks[index].finished"
              :v-model="linkObj"
              label="Have you finished the video?"
              @click="videoComplete(index)"
            >
            </v-checkbox>
          </div>
          <div class="d-flex justify-center mt-2 mb-4">
            <v-btn
              depressed
              outlined
              x-small
              a
              href="https://www.youtube.com/channel/UCNQZIRiTx54gNzMji3iHgJg"
              target="_blank"
              >Subscribe to PilotCity YouTube</v-btn
            >
          </div>
        </v-expansion-panel-content>
      </v-expansion-panel>
    </v-expansion-panels>

    <br />
    <br />
    <div class="module-default__scope">
      <v-btn x-large depressed outlined :disabled="finishButtonDisabled === 1" :loading="loading" @click="process()">Finish Activity</v-btn>
      <v-alert v-if="success || error" class="mt-3" :type="success ? 'success' : 'error'">{{
        message
      }}</v-alert>
    </div>
  </v-container>
</template>

<script lang="ts">
import { computed, defineComponent, ref } from '@vue/composition-api';
import { loading } from 'pcv4lib/src'
import MongoDoc from '../types';
import Instruct from './ModuleInstruct.vue';

export default defineComponent({
  name: 'ModuleDefault',
  components: {
    Instruct
  },
  props: {
    value: {
      required: true,
      type: Object as () => MongoDoc
    }
  },
  setup(props, ctx) {
    const programDoc = computed({
      get: () => props.value,
      set: newVal => {
        ctx.emit('input', newVal);
      }
    });
    let index = programDoc.value.data.adks.findIndex(function findResearchObj(obj) {
      return obj.name === 'train';
    });
    if (index === -1)
      index =
        programDoc.value.data.adks.push({
          name: 'train'
        }) - 1;
    const trainData = computed(() => programDoc.value.data.adks.find(adk => adk.name === 'train'));
    const setupInstructions = ref({
      description: '',
      instructions: ['', '', '']
    });
    const showInstructions = ref(true);
    const finishButtonDisabled = ref(1);
    function videoComplete(index: number) {
      if (trainData.value.videoLinks[index + 1]) {
        trainData.value.videoLinks[index + 1].disabled = !trainData.value.videoLinks[index + 1]
          .disabled;
      }
      if (!trainData.value.videoLinks[index].finished && trainData.value.videoLinks[index + 1]) {
        for (let i = index; i < trainData.value.videoLinks.length - 1; i += 1) {
          trainData.value.videoLinks[i + 1].disabled = true;
          finishButtonDisabled.value = 1;
        }
      }
      const lastVideoLink = trainData.value.videoLinks[trainData.value.videoLinks.length - 1];
      if (lastVideoLink.finished && !lastVideoLink.disabled) {
        finishButtonDisabled.value = 0;
      } else {
        finishButtonDisabled.value = 1;
      }
    }
    function getYoutubeId(url: string) {
      const regExp = /^.*((youtu.be\/)|(v\/)|(\/u\/\w\/)|(embed\/)|(watch\?))\??v?=?([^#&?]*).*/;
      const match = url.match(regExp);
      return match && match[7].length === 11 ? match[7] : false;
    }
    return {
      finishButtonDisabled,
      setupInstructions,
      showInstructions,
      programDoc,
      trainData,
      getYoutubeId,
      videoComplete,
      ...loading(
        () => programDoc.value.update(
          () => ({
            isComplete: true,
            adkIndex: index
          })),
        'Saved',
        'Something went wrong, try again later'
      )
    };
  }
});
</script>

<style lang="scss">
.v-expansion-panel-content__wrap {
  // padding: 0px !important;
}
.module-default {
  &__video-title {
    font-weight: 800;
    line-height: 25px !important;
    font-size: 16px !important;
    color: #404142;
  }
  &__none {
    border-radius: 5px;
    // border: 1px solid #dedede;
    height: 100px;
    text-align: center;
    background-color: #dedede;
    font-weight: 700;
    color: #ffffff;
    font-size: 18px;
    padding-top: 35px;
  }

  &__collapse-divider {
    margin-top: 15px;
    // margin-bottom: 75px;
    // margin-bottom: none !important;
    margin-right: none;
    margin-left: none;
    padding-right: none;
    padding-left: none;
    width: 100%;
  }

  &__collapse-title {
    color: #dedede;
    text-align: center;
    text-transform: uppercase;
    font-weight: 900;
    letter-spacing: 1px;
    font-size: 13px;
    //  text-uppercase font-weight-bold text-subtitle-2 text-center
  }

  &__container {
    width: 100%;
  }
  &__playlist {
    width: 100%;
  }

  &__playlist-panel {
    border-bottom: 1px solid #dedede;
  }

  &__video-content-panel {
  }
}

.v-expansion-panel-content__wrap {
  padding: 0px !important;
}
</style>
