<template>
  <ValidationObserver v-slot="{ invalid }" slim>
    <v-container class="module-edit">
      <div :ref="body" class="module-edit__body">
        <div class="module-edit__container">
          <!-- <div class="module-edit__video">Name</div>
          <div class="module-edit__link">Link</div>
          <div class="module-edit__required">Required</div> -->
        </div>
        <div
          v-for="(i, linkIndex) in programDoc.data.adks[index].videoLinks"
          :key="linkIndex"
          class="module-edit__inputs"
        >
          <div class="module-edit__inputs-video">
            <validation-provider v-slot="{ errors }" slim rules="required">
              <v-text-field
                v-model="i.name"
                rounded
                :error-messages="errors"
                label="Video Name"
                outlined
              >
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
                v-model.trim="i.link"
                rounded
                label="Video Link"
                :error-messages="errors"
                outlined
              ></v-text-field>
            </validation-provider>
          </div>
          <div class="module-edit__inputs-required">
            <!-- <v-checkbox v-model="i.required"></v-checkbox> -->
            <v-btn
              rounded
              :disabled="linkIndex == 0"
              x-large
              outlined
              @click="removeIndex(linkIndex)"
              >Delete</v-btn
            >
          </div>
        </div>

        <div class="module-edit__add">
          <v-btn
            rounded
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

        <div>
          <v-btn
            class="mt-12"
            x-large
            outlined
            :disabled="invalid"
            depressed
            :loading="loading"
            @click="process()"
            >Save</v-btn
          >
        </div>
        <v-alert v-if="success || error" class="mt-3" :type="success ? 'success' : 'error'">{{
          message
        }}</v-alert>
      </div>
    </v-container>
  </ValidationObserver>
</template>

<script lang="ts">
import { computed, defineComponent, ref } from '@vue/composition-api';
import { createLoader } from 'pcv4lib/src';
import MongoDoc from '../types';

export default defineComponent({
  name: 'ModuleSetup',
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
    const initTrainSetup = {
      videoLinks: [
        {
          name: '',
          link: ''
        }
      ]
    };
    programDoc.value.data.adks[index] = {
      ...initTrainSetup,
      ...programDoc.value.data.adks[index]
    };
    // Reactivity Handling
    const body = ref(0);
    function populate() {
      const train1 = ref({
        name: '',
        link: ''
      });
      programDoc.value.data.adks[index].videoLinks.push(train1.value);
      body.value += 1;
    }
    function removeIndex(linkIndex: number) {
      programDoc.value.data.adks[index].videoLinks.splice(linkIndex, 1);
      body.value += 1;
    }
    // Handle Save

    return {
      populate,
      index,
      programDoc,
      body,
      removeIndex,
      ...createLoader(programDoc.value.update, 'Saved', 'Something went wrong, try again later')
    };
  }
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
