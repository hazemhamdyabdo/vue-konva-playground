<script lang="ts" setup>
import { ref, onMounted } from 'vue'

const imageUrl =
  'https://upload.wikimedia.org/wikipedia/commons/thumb/3/3f/Fronalpstock_big.jpg/800px-Fronalpstock_big.jpg'
const imageObj = ref<HTMLImageElement | null>(null)

const stageWidth = 600
const stageHeight = 400

const boxes = ref([{ id: 1, x: 120, y: 100, w: 150, h: 100, label: 'Object A' }])

onMounted(() => {
  const img = new Image()
  img.src = imageUrl
  img.onload = () => {
    imageObj.value = img
  }
})
</script>

<template>
  <div>
    <h2>🖼️ Image Annotation</h2>
    <v-stage :config="{ width: stageWidth, height: stageHeight }">
      <v-layer>
        <v-image :config="{ image: imageObj }" />

        <v-rect
          v-for="box in boxes"
          :key="box.id"
          :config="{
            x: box.x,
            y: box.y,
            width: box.w,
            height: box.h,
            stroke: 'red',
            strokeWidth: 2,
            draggable: true,
          }"
        />
      </v-layer>
    </v-stage>
  </div>
</template>

<style scoped>
h2 {
  margin-bottom: 10px;
}
</style>
