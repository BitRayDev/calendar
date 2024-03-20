<template>
  <Calendar v-model="date" :locale="locale"/>
  <p>{{ date?.toLocaleString(locale) }}</p>
  <button @click="locale = locales[(locales.indexOf(locale) + 1) % locales.length]">{{ locale }}</button>
</template>

<script setup>
import Calendar from "./components/Calendar.vue";
import {ref} from "vue";

const locales = ['en-US', 'ru-RU'];
const locale = ref(locales[0]);

const date = ref();
</script>

<style lang="scss">
</style>
