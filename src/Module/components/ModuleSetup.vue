<template>
  <ValidationObserver v-slot="{ invalid }" slim>
    <v-container class="module-edit">
      <div class="module-edit__container">
        <v-expansion-panels class="module-default__playlist">
          <v-expansion-panel>
            <v-expansion-panel-header disable-icon-rotate>
              Vision Podcast
              <template v-slot:actions>
                <v-icon color="teal">mdi-check</v-icon>
              </template>
            </v-expansion-panel-header>
            <v-expansion-panel-content>
              <div class="module-setup__two-column">
                <div class="module-setup__left-column">
                  <validation-provider v-slot="{ errors }" slim rules="required">
                    <v-text-field
                      v-model="visionVideo"
                      :error-messages="errors"
                      outlined
                      label="Video Name"
                    ></v-text-field>
                  </validation-provider>
                </div>
                <div class="module-setup__right-column">
                  <validation-provider v-slot="{ errors }" slim rules="required">
                    <v-text-field
                      v-model="visionLink"
                      :error-messages="errors"
                      outlined
                      label="Link"
                    ></v-text-field>
                  </validation-provider>
                </div>
              </div>
            </v-expansion-panel-content>
          </v-expansion-panel>
          <v-expansion-panel>
            <v-expansion-panel-header>Product Demonstration</v-expansion-panel-header>
            <v-expansion-panel-content>
              <div class="module-setup__two-column">
                <div class="module-setup__left-column">
                  <validation-provider v-slot="{ errors }" slim rules="required">
                    <v-text-field
                      v-model="productVideo"
                      outlined
                      :error-messages="errors"
                      label="Video Name"
                    ></v-text-field>
                  </validation-provider>
                </div>
                <div class="module-setup__right-column">
                  <validation-provider v-slot="{ errors }" slim rules="required">
                    <v-text-field
                      v-model="productLink"
                      :error-messages="errors"
                      outlined
                      label="Link"
                    ></v-text-field>
                  </validation-provider>
                </div>
              </div>
            </v-expansion-panel-content>
          </v-expansion-panel>
          <v-expansion-panel>
            <v-expansion-panel-header>Industry Panel</v-expansion-panel-header>
            <v-expansion-panel-content>
              <div class="module-setup__two-column">
                <div class="module-setup__left-column">
                  <validation-provider v-slot="{ errors }" slim rules="required">
                    <v-text-field
                      v-model="industryVideo"
                      :error-messages="errors"
                      outlined
                      label="Video Name"
                    ></v-text-field>
                  </validation-provider>
                </div>
                <div class="module-setup__right-column">
                  <validation-provider v-slot="{ errors }" slim rules="required">
                    <v-text-field
                      v-model="industryLink"
                      :error-messages="errors"
                      outlined
                      label="Link"
                    ></v-text-field>
                  </validation-provider>
                </div>
              </div>
            </v-expansion-panel-content>
          </v-expansion-panel>
        </v-expansion-panels>
        <br />
        <br />
        <v-btn x-large outlined :disabled="invalid" depressed>Save</v-btn>
      </div>
    </v-container>
  </ValidationObserver>
</template>

<script lang="ts">
import { defineComponent } from '@vue/composition-api';

export default defineComponent({
  name: 'ModuleSetup',
  data() {
    return {
      visionVideo: '',
      visionLink: '',
      productVideo: '',
      productLink: '',
      industryVideo: '',
      industryLink: ''
    };
  }
  // setup() {
  //   const setupInstructions = ref(['']);
  //   const setup = reactive({
  //     visionVideo: '',
  //     visionLink: '',
  //     productVideo: '',
  //     productLink: '',
  //     industryVideo: '',
  //     industryLink: ''
  //   });
  //   // const setup = reactive({});
  //   return {
  //     setupInstructions,
  //     ...toRefs(setup)
  //   };
  // }
});
</script>

<style lang="scss">
.module-setup {
  &__two-column {
    flex-direction: row;
    display: flex;
  }
  &__left-column {
    width: 100%;
  }
  &__right-column {
    width: 100%;
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
}
</style>
