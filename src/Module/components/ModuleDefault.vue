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
            <template #default="{ open }">
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
              <v-icon color="grey lighten-2" class="d-flex justify-center"> mdi-chevron-up </v-icon>
            </div>
          </v-expansion-panel-content>
        </v-expansion-panel>
      </v-expansion-panels>
    </div>
    <v-progress-linear
      class="mt-3"
      color="#dedede"
      height="2"
      value="100"
      buffer-value="100"
      stream
    />

    <v-expansion-panels v-if="trainData" tile accordion flat class="module-default__playlist">
      <v-expansion-panel
        v-for="(linkObj, index) in trainAdkData.trainProgress"
        :key="index"
        class="module-default__playlist-panel"
        :disabled="!linkObj.unlocked"
      >
        <v-expansion-panel-header
          :class="{ 'grey--text text--lighten-2': linkObj.unlocked === false }"
          disable-icon-rotate
          class="module-default__video-title"
        >
          {{ linkObj.name }}
          <template #actions>
            <v-icon v-if="linkObj.unlocked && !linkObj.completed" color="warning">
              mdi-alert-circle
            </v-icon>
            <v-icon v-if="linkObj.completed" color="teal">mdi-check</v-icon>
          </template>
          <v-icon v-if="!linkObj.unlocked" style="position: absolute; right: 24px" color="error">
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
              v-model="linkObj.completed"
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
              href="https://www.youtube.com/pilotcity"
              target="_blank"
              >Subscribe to PilotCity YouTube</v-btn
            >
          </div>
        </v-expansion-panel-content>
      </v-expansion-panel>
    </v-expansion-panels>

    <br />
    <br />
    <div class="module-default__scope justify-center">
      <v-btn
        class="mr-2"
        x-large
        rounded
        outlined
        depressed
        :loading="saveLoading"
        @click="saveProcess">
        Save
      </v-btn>
      <v-btn
        class="ml-2"
        x-large
        depressed
        rounded
        outlined
        :disabled="!finishButtonDisabled || userType === 'stakeholder'"
        :loading="loading"
        @click="process()"
        >Complete</v-btn
      >
      <v-alert v-if="success || error" class="mt-3" :type="success ? 'success' : 'error'"
        >{{ message }}
      </v-alert>
      <v-alert
        v-if="saveSuccess || saveError"
        class="mt-3"
        :type="saveSuccess ? 'success' : 'error'"
        >{{ saveMessage }}</v-alert
      >
    </div>
  </v-container>
</template>

<script lang="ts">
import { computed, defineComponent, ref } from '@vue/composition-api';
import { loading, getModAdk } from 'pcv4lib/src';
import MongoDoc from '../types';

export default defineComponent({
  name: 'ModuleDefault',
  props: {
    value: {
      required: true,
      type: Object as () => MongoDoc
    },
    studentDoc: {
      required: true,
      type: Object as () => MongoDoc
    },
    userType: {
      required: true
    }
  },
  setup(props, ctx) {
    const trainData = computed(() => props.value.data.adks.find(obj => obj.name === 'train'));
    const { adkData: trainAdk, adkIndex } = getModAdk(
      props,
      ctx.emit,
      'train',
      {
        trainProgress: (trainData.value!.videoLinks as any[]).map((obj: any) => ({
          ...obj,
          unlocked: false,
          completed: false
        }))
      },
      'studentDoc',
      'inputStudentDoc'
    );
    const trainAdkData = ref(trainAdk.value);
    trainAdkData.value.trainProgress[0].unlocked = true;
    const setupInstructions = ref({
      description: '',
      instructions: ['', '', '']
    });
    const showInstructions = ref(true);
    const {
      loading: saveLoading,
      process: saveProcess,
      message: saveMessage,
      error: saveError,
      success: saveSuccess
    } = loading(() => props.studentDoc.update(), 'Saved', 'Something went wrong, try again later');
    const finishButtonDisabled = ref(
      trainAdkData.value.trainProgress.every((item: any) => (item.completed ? true : null))
    );
    function videoComplete(index: number) {
      if (trainAdkData.value.trainProgress[index + 1]) {
        trainAdkData.value.trainProgress[index + 1].unlocked = !trainAdkData.value.trainProgress[
          index + 1
        ].unlocked;
      }
      if (
        !trainAdkData.value.trainProgress[index].completed &&
        trainAdkData.value.trainProgress[index + 1]
      ) {
        for (let i = index; i < trainAdkData.value.trainProgress.length - 1; i += 1) {
          trainAdkData.value.trainProgress[i + 1].unlocked = false;
          trainAdkData.value.trainProgress[i + 1].completed = false;
        }
      }
      const lastVideoLink =
        trainAdkData.value.trainProgress[trainAdkData.value.trainProgress.length - 1];
      if (lastVideoLink.completed && lastVideoLink.unlocked) {
        finishButtonDisabled.value = true;
      } else {
        finishButtonDisabled.value = false;
      }
    }
    function getYoutubeId(url: string) {
      const regExp = /^.*((youtu.be\/)|(v\/)|(\/u\/\w\/)|(embed\/)|(watch\?))\??v?=?([^#&?]*).*/;
      const match = url.match(regExp);
      return match && match[7].length === 11 ? match[7] : false;
    }
    return {
      finishButtonDisabled,
      showInstructions,
      setupInstructions,
      trainData,
      saveMessage,
      saveError,
      saveLoading,
      saveSuccess,
      trainAdkData,
      status,
      saveProcess,
      getYoutubeId,
      videoComplete,
      ...loading(
        () =>
          props.studentDoc.update(() => ({
            isComplete: true,
            adkIndex
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
