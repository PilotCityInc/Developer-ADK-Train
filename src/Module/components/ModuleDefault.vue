<template>
  <v-container class="module-default__container pa-0 mt-0">
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
      class="module-default__collapse-divider"
      color="#dedede"
      height="2"
      value="100"
      buffer-value="100"
      stream
    />
    <div class="module-edit__container">
      <v-expansion-panels class="module-default__playlist">
        <v-expansion-panel>
          <v-expansion-panel-header disable-icon-rotate>
            Vision Podcast
            <template v-slot:actions>
              <v-icon color="teal">mdi-check</v-icon>
            </template>
          </v-expansion-panel-header>
          <v-expansion-panel-content></v-expansion-panel-content>
        </v-expansion-panel>
        <v-expansion-panel>
          <v-expansion-panel-header>Product Demonstration</v-expansion-panel-header>
          <v-expansion-panel-content></v-expansion-panel-content>
        </v-expansion-panel>
        <v-expansion-panel>
          <v-expansion-panel-header>Industry Panel</v-expansion-panel-header>
          <v-expansion-panel-content></v-expansion-panel-content>
        </v-expansion-panel>
      </v-expansion-panels>
    </div>
    <br />
    <br />
    <div><v-btn x-large outlined depressed>Finish</v-btn></div>
  </v-container>
</template>

<script lang="ts">
import { ref } from '@vue/composition-api';
import Instruct from './ModuleInstruct.vue';

export default {
  name: 'ModuleDefault',
  components: {
    Instruct
  },
  apollo: {},
  data() {
    const setupInstructions = ref({
      description: '',
      instructions: ['', '', '']
    });
    const showInstructions = ref(true);
    return {
      setupInstructions,
      showInstructions
    };
  }
};
</script>

<style lang="scss">
.module-default {
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
    margin-bottom: 75px;
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
}
</style>
