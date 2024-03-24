<script setup lang="ts">
import { computed, ref } from 'vue';

const DEFAULT_ROTATION = '0deg';

const totalSeconds = 60;
let currentSeconds = totalSeconds;
let isRunning = ref(false);
let currentRotation = ref(DEFAULT_ROTATION);

const rotationStyle = computed(() => {
  return {
        '--degrees': currentRotation.value
      }
});

const formatTime = (seconds: number) => {
  const minutes = Math.floor(seconds / 60);
  const newSeconds = seconds % 60;

  return `${minutes.toString().padStart(2, '0')}:${newSeconds
    .toString()
    .padStart(2, '0')}`;
}

const calculateRotation = () => `${360 - (currentSeconds / totalSeconds) * 360}deg`;

let timeElapsed = ref(formatTime(totalSeconds));

const run = () => {
  if (isRunning.value) {
    currentSeconds -= 1;
    if (currentSeconds <= 0) {
      clearInterval(timerInterval);
      reset();
    }

    timeElapsed.value = formatTime(currentSeconds);
    currentRotation.value = calculateRotation();
  }
}

const start = () => {
  isRunning.value = !isRunning.value;
}

const reset = () => {
  isRunning.value = false;
  currentSeconds = totalSeconds;
  timeElapsed.value = formatTime(currentSeconds);
  currentRotation.value = DEFAULT_ROTATION;
}

const timerInterval = setInterval(run, 1000);
</script>

<template>
    <!-- Circle -->
      <div
        id="conic"
        class="bg-conic flex items-center justify-center w-60 h-60 mx-auto my-10 rounded-full relative"
        :style="rotationStyle"
      >
        <p id="timer" class="text-blue-200 relative text-5xl z-10" >{{ timeElapsed }}</p>

        <!-- Inner circle & line -->
        <div
          class="w-[calc(100%-4px)] aspect-square bg-gray-800 rounded-full absolute inset-[2px]"
        ></div>
        <!-- Hand -->
        <div class="hand h-1/2 absolute top-0">
          <span
            class="w-2 h-2 bg-amber-500 rounded-full absolute -top-1 -left-1"
          ></span>
        </div>
      </div>

      <!-- Buttons -->
      <div class="flex justify-center gap-6">
        <button
          id="reset"
          class="flex justify-center items-center w-10 h-10 bg-red-300 rounded-full"
          @click="reset"
        >
          <i class="fas fa-refresh"></i>
        </button>

        <button
          id="play"
          :class="['flex justify-center items-center w-10 h-10 bg-green-300 rounded-full group', { 'bg-yellow-300': isRunning }]"
          @click="start"
        >
          <i :class="[isRunning ? 'fas fa-pause' : 'fas fa-play']"></i>
        </button>
      </div>
</template>