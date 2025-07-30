<template>
  <v-app>
    <v-main>
      <HelloWorld />
    </v-main>
  </v-app>
</template>

<script setup lang="ts">
import { useLocale } from 'vuetify'

const { current } = useLocale()

function changeLocale(locale: string) {
  current.value = locale
}
</script>
