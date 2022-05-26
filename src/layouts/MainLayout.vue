<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated class="q-pt-lg">
      <q-toolbar>
        <q-toolbar-title> Quasar Grid Table </q-toolbar-title>

        <div></div>
      </q-toolbar>
    </q-header>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { defineComponent, ref } from "vue";

export default defineComponent({
  name: "MainLayout",

  setup() {},
});
</script>
