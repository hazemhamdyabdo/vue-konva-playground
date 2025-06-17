<script lang="ts" setup>
import { ref, onMounted, watch } from 'vue'

const videoUrl = 'https://media.w3.org/2010/05/sintel/trailer.mp4'
const videoRef = ref<HTMLVideoElement | null>(null)

const stageWidth = ref(800)
const stageHeight = ref(450)

const box = ref({ x: 100, y: 100, width: 150, height: 100 })
const trackedKeyframes = ref<
  { time: number; x: number; y: number; width: number; height: number }[]
>([])

function saveKeyframe() {
  const video = videoRef.value
  if (!video) return

  trackedKeyframes.value.push({
    time: parseFloat(video.currentTime.toFixed(2)),
    x: box.value.x,
    y: box.value.y,
    width: box.value.width,
    height: box.value.height,
  })
}

function updateBoxFromCurrentTime() {
  const video = videoRef.value
  if (!video) return

  const currentTime = parseFloat(video.currentTime.toFixed(2))
  const frame = trackedKeyframes.value.find((f) => f.time === currentTime)
  if (frame) {
    box.value = { ...frame }
  }
}

onMounted(() => {
  const video = videoRef.value
  if (!video) return

  video.addEventListener('timeupdate', updateBoxFromCurrentTime)
})
</script>

<template>
  <div style="position: relative; display: inline-block">
    <video
      ref="videoRef"
      :width="stageWidth"
      :height="stageHeight"
      controls
      autoplay
      playsinline
      style="display: block"
    >
      <source :src="videoUrl" type="video/mp4" />
    </video>

    <div
      style="position: absolute; top: 0; left: 0"
      :style="{ width: stageWidth + 'px', height: stageHeight + 'px' }"
    >
      <v-stage :config="{ width: stageWidth, height: stageHeight }">
        <v-layer>
          <v-rect
            :config="{
              ...box,
              stroke: 'red',
              strokeWidth: 2,
              draggable: true,
            }"
            @dragend="
              (e) => {
                const shape = e.target
                box.x = shape.x()
                box.y = shape.y()
              }
            "
          />
        </v-layer>
      </v-stage>
    </div>
  </div>

  <button @click="saveKeyframe" style="margin-top: 1rem">💾 Save Keyframe</button>

  <pre>{{ trackedKeyframes }}</pre>
</template>
