<script setup>
import { ref } from 'vue'
import { ArrowPathIcon, PauseIcon, PlayIcon } from '@heroicons/vue/24/outline'
import { formatSeconds } from '../functions'
import {
  BUTTON_TYPE_SUCCESS,
  BUTTON_TYPE_WARNING,
  BUTTON_TYPE_DANGER,
  MILISECONDS_IN_SECOND
} from '../constants'
import { isHourValid, isNumber } from '@/validators'
import BaseButton from './BaseButton.vue'

const props = defineProps({
  seconds: {
    default: 0,
    type: Number,
    validator: isNumber
  },
  hour: {
    required: true,
    type: Number,
    validator: isHourValid
  }
})

const emit = defineEmits({
  updateSeconds: isNumber
})

const seconds = ref(props.seconds)
const isRunning = ref(false)

const isStartButtonDisabled = props.hour !== new Date().getHours()

function start() {
  isRunning.value = setInterval(() => {
    emit('updateSeconds', 1)
    seconds.value++
  }, MILISECONDS_IN_SECOND)
}

function stop() {
  clearInterval(isRunning.value)
  isRunning.value = false
}

function reset() {
  stop()
  emit('updateSeconds', -seconds.value)

  seconds.value = 0
}
</script>

<template>
  <div class="flex w-full gap-2">
    <BaseButton :type="BUTTON_TYPE_DANGER" :disabled="!seconds" @click="reset">
      <ArrowPathIcon class="h-5" />
    </BaseButton>
    <div class="flex flex-grow items-center rouded bg-gray-100 px-2 font-mono text-3xl">
      {{ formatSeconds(seconds) }}
    </div>
    <BaseButton v-if="isRunning" :type="BUTTON_TYPE_WARNING" @click="stop">
      <PauseIcon class="h-5" />
    </BaseButton>
    <BaseButton v-else :type="BUTTON_TYPE_SUCCESS" :disabled="isStartButtonDisabled" @click="start">
      <PlayIcon class="h-5" />
    </BaseButton>
  </div>
</template>
