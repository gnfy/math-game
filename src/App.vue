<script setup>
import { ref } from 'vue'
import StartPage from './components/StartPage.vue'
import BalancePage from './components/BalancePage.vue'

const pageStatus = ref('start')

function onChangeStatus(status) {
  pageStatus.value = status
}

</script>

<template>
  <StartPage @onChangeStatus="onChangeStatus" v-if="pageStatus === 'start'" />
  <BalancePage v-else-if="pageStatus === 'balance'" />
</template>

<style scoped></style>
