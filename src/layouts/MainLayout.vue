<template>
  <q-layout view="lHh Lpr lFf" >
    <q-header elevated>
      <q-bar v-if="mode === 'electron'" class="q-electron-drag">
        <q-icon name="laptop_chromebook" />
        <div>PDF-fill</div>

        <q-space />

        <q-btn dense flat icon="minimize" @click="minimize" />
        <q-btn dense flat icon="crop_square" @click="toggleMaximize" />
        <q-btn dense flat icon="close" @click="closeApp" />
      </q-bar>

    </q-header>
    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script lang="ts" setup>
import { ref } from "vue";
let mode = process?.env?.MODE;
// @ts-ignore
let myWindowAPI = window.myWindowAPI;
function minimize () {
  if (mode === 'electron') {
    myWindowAPI.minimize()
  }
}

function toggleMaximize () {
  if (mode === 'electron') {
    myWindowAPI.toggleMaximize()
  }
}

function closeApp () {
  if (mode === 'electron') {
    myWindowAPI.close()
  }
}

const leftDrawerOpen = ref(false)



function toggleLeftDrawer () {
  leftDrawerOpen.value = !leftDrawerOpen.value
}
</script>
