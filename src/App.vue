<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-toolbar>
        <q-toolbar-title>
          OCR Text Recognition
        </q-toolbar-title>

        <div>Quasar v{{ $q.version }}</div>
      </q-toolbar>
    </q-header>

    <q-page-container>
      <router-view> </router-view>
    </q-page-container>

    

  </q-layout>
</template>

<script>
import { ref } from "vue";
//import TheFooter from './components/TheFooter.vue'
export default {
  name: "LayoutDefault",

  components: {
    //"the-footer": TheFooter
  },

  setup() {
    return {
      leftDrawerOpen: ref(false),
    };
  },
};
</script>
