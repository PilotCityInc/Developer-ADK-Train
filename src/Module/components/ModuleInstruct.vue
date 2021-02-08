<template>
  <ValidationObserver v-slot="{}" slim>
    <!-- <ValidationObserver v-slot="{ invalid }" slim> -->
    <!--  TODO: make the inputs into actual components -->
    <v-container class="module-instruct">
      <div class="module-instruct__container">
        <div class="module-instruct__description">
          <div class="module-instruct__description-label">
            <span>Goal</span>
          </div>

          <v-textarea
            v-model="goal"
            row-height="3"
            rows="3"
            outlined
            class="font-weight-bold text-body-1"
            hide-details
            dense
            auto-grow
            disabled
          ></v-textarea>
        </div>
        <div class="module-instruct__instructions">
          <div class="module-instruct__description-label">
            <span>Instructions</span>
          </div>
          <div
            v-for="(i, index) in trainInstructions"
            :key="index"
            class="module-instruct__instructions-item"
          >
            <v-avatar
              size="35"
              color="white"
              class="module-instruct__instructions-av font-weight-bold text-caption d-none d-sm-flex"
            >
              {{ index + 1 }}
            </v-avatar>

            <validation-provider v-slot="{ errors }" slim rules="required">
              <v-textarea
                v-model="trainInstructions[index]"
                row-height="3"
                rows="1"
                outlined
                hide-details
                dense
                :error-messages="errors"
                class="font-weight-bold text-body-1"
                auto-grow
                disabled
              ></v-textarea>
            </validation-provider>
          </div>

          <!-- <div>
            <v-btn
              class="module-instruct__instructions-add font-weight-black text-body-1"
              depressed
              color="white"
              :disabled="invalid"
              :ripple="false"
              @click="populate()"
            >
              <v-icon class="module-instruct__instructions-add-icon"> mdi-plus </v-icon>
            </v-btn>
          </div> -->
        </div>
      </div>
    </v-container>
  </ValidationObserver>
</template>

<script lang="ts">
import { ref } from '@vue/composition-api';

export default {
  name: 'ModuleInstruct',
  setup() {
    const trainInstructions = ref([
      'Watch one video after another',
      'Digest, take notes and repeat until complete'
    ]);
    const goal = ref([
      'To watch, learn and understand the details of your employer, their project scope and industry'
    ]);
    function populate() {
      trainInstructions.value.push('');
    }
    return { trainInstructions, populate, goal };
  }
};
</script>

<style lang="scss">
.module-instruct__instructions-av {
  margin-right: 3%;
}
</style>
