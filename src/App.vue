<template>
  <v-app>
    <Module
      v-model="programDocStub"
      :user-type="userTypeStub"
      :student-doc="studentDocStub"
      @inputStudentDoc="studentDocStub = $event"
    />
  </v-app>
</template>

<script lang="ts">
// import ApolloExample from './components/ApolloExample.vue';
import { defineComponent, Ref, ref } from '@vue/composition-api';
import Module from './Module/Module.vue';
import MongoDoc from './Module/types';

export default defineComponent({
  name: 'App',

  components: {
    Module
  },
  setup() {
    const programDocStub: Ref<MongoDoc> = ref({
      data: {
        adks: [
          {
            videoLinks: [
              { name: 'awewea', link: 'eawweaew', required: true },
              { name: 'aweewa', link: 'waeewa', required: false }
            ],
            name: 'train'
          }
        ]
      },
      update: () => {
        return new Promise((resolve, reject) => {
          setTimeout(() => {
            resolve(true);
            // reject(new Error('REJECTED'));
          }, 3000);
        });
      },
      changeStream: {}
    });
    const studentDocStub: Ref<MongoDoc | null> = ref({
      data: {
        adks: []
      },
      update: () => {
        return new Promise((resolve, reject) => {
          setTimeout(() => {
            resolve(true);
            // reject(new Error('REJECTED'));
          }, 3000);
        });
      },
      changeStream: {}
    });
    const userTypeStub: 'organizer' | 'participant' | 'stakeholder' = 'participant';
    if (userTypeStub === 'organizer') studentDocStub.value = null;
    return {
      programDocStub,
      userTypeStub,
      studentDocStub
    };
  }
});
</script>
