<template>
  <ValidationObserver v-slot="{ invalid }" slim>
    <v-container class="module-edit">
      <div class="module-edit__body">
        <div class="module-edit__container">
          <!-- <div class="module-edit__video">Name</div>
          <div class="module-edit__link">Link</div>
          <div class="module-edit__required">Required</div> -->
        </div>

        <div v-for="(i, index) in train" :key="index" class="module-edit__inputs">
          <div class="module-edit__inputs-video">
            <validation-provider v-slot="{ errors }" slim rules="required">
              <v-text-field v-model="i.name" :error-messages="errors" label="Video Name" outlined>
              </v-text-field>
              <!-- <div>{{ i.name }}</div> -->
            </validation-provider>
          </div>
          <div class="module-edit__inputs-link">
            <validation-provider
              v-slot="{ errors }"
              slim
              :rules="{
                required: true
              }"
            >
              <v-text-field
                v-model="i.link"
                label="Video Link"
                :error-messages="errors"
                outlined
              ></v-text-field>
            </validation-provider>
          </div>
          <div class="module-edit__inputs-required">
            <!-- <v-checkbox v-model="i.required"></v-checkbox> -->
            <v-btn x-large outlined>Delete</v-btn>
          </div>
        </div>

        <div class="module-edit__add">
          <v-btn
            x-large
            class="module-edit__add-button"
            depressed
            outlined
            :disabled="invalid"
            :ripple="false"
            @click="populate()"
          >
            <v-icon class="module-edit__add-icon"> mdi-plus </v-icon>
          </v-btn>
        </div>
      </div>

      <!-- <div class="module-edit__container">
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
      </div> -->
    </v-container>
  </ValidationObserver>
</template>

<script lang="ts">
import { defineComponent } from '@vue/composition-api';

export default defineComponent({
  name: 'ModuleSetup',
  data() {
    return {
      train: [
        {
          visionVideo: '',
          visionLink: '',
          productVideo: '',
          productLink: '',
          industryVideo: '',
          industryLink: ''
        }
      ]
    };
  },
  methods: {
    populate() {
      const train1 = {
        name: '',
        link: '',
        required: false
      };
      this.train.push(train1);
    }
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

.module-edit {
  &__body {
    width: 100%;
    display: flex;
    flex-direction: column;
    // padding-left: 5%;
    justify-content: center;
    align-items: center;
  }
  &__container {
    display: flex;
    flex-direction: row;
    width: 100%;
  }

  &__video {
    width: 100%;
  }
  &__link {
    // margin-right: 15%;
    width: 100%;
  }
  &__required {
    // padding-left: 20%;
    width: 100%;
  }
  &__inputs {
    width: 100%;
    margin-top: 1%;
    display: flex;
    flex-direction: row;
    &-video {
      display: flex;
      width: 100%;
      // margin-right: 9%;
    }
    &-link {
      display: flex;
      width: 100%;
      margin-left: 3%;
    }
    &-required {
      display: flex;
      // width: 100%;
      margin-left: 3%;
      justify-content: center;
    }
  }
  &__add {
    display: flex;
    width: 100%;
    // padding-right: 15%;
    &-button {
      width: 100%;
    }
  }
}
</style>
