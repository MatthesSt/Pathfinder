<template>
  <main class="m-2">
    <div v-for="(col, colIndex) of map" class="map-grid">
      <div
        class="tile"
        v-for="(tile, tileIndex) of col"
        :class="colIndex == seeker.y && tileIndex == seeker.x ? 'seekerPos' : colIndex == finish.y && tileIndex == finish.x ? 'finishPos' : ''"
      >
        {{ tileIndex }},{{ colIndex }}
      </div>
    </div>
    <div class="row g-2 m-2">
      <div class="col-6 d-flex align-items-center">
        <label class="me-2">width:</label>
        <input type="number" inputmode="numeric" v-model="width" />
      </div>
      <div class="col-6 d-flex align-items-center">
        <label class="me-2">height:</label>
        <input type="number" inputmode="numeric" v-model="height" />
      </div>
      <div class="col-6 d-flex align-items-center">
        <label class="me-2">apm:</label>
        <input type="number" inputmode="numeric" v-model="apm" />
      </div>
    </div>
    <div class="">
      <Button type="button" @click.stop="start()">Start</Button>
      <Button type="button" @click.stop="stop()">Stop</Button>
    </div>
    {{ seeker }}
  </main>
</template>
<script setup lang="ts">
import { computed, ref } from 'vue';
import { Button } from 'custom-mbd-components';

const finish = ref({
  x: 5,
  y: 3,
});
const seeker = ref({
  x: 5,
  y: 0,
});
const width = ref(10);
const height = ref(10);
const map = computed(() => {
  return new Array(+height.value || 1).fill(new Array(+width.value || 1));
});

const moves = ref<number[]>([]);
const running = ref(false);
const seekerInterval = ref<number | null>(null);
const apm = ref(60);

function start() {
  if (running.value) return (running.value = !running.value);
  pathfinder();
}
function stop() {
  clearInterval(seekerInterval.value!);
}
function pathfinder() {
  console.log('start');
  seekerInterval.value = setInterval(() => {
    move();
  }, (60 / apm.value) * 1000);
}
function move() {
  let x = +(Math.random() > 0.5) * (Math.random() > 0.5 ? -1 : 1);
  let y = +(Math.random() > 0.5) * (Math.random() > 0.5 ? -1 : 1);
  moves.value.push(x, y);
  if (x && y) Math.random() > 0.5 ? (x = 0) : (y = 0);
  seeker.value.x += x;
  seeker.value.y += y;
  seeker.value.x < 0 ? (seeker.value.x = 0) : seeker.value.x > width.value - 1 ? (seeker.value.x = width.value - 1) : null;
  seeker.value.y < 0 ? (seeker.value.y = 0) : seeker.value.y > height.value - 1 ? (seeker.value.y = height.value - 1) : null;
}
</script>

<style scoped lang="scss">
.map-grid {
  display: grid;
  grid-template-columns: repeat(v-bind(width), 1fr);
}
.tile {
  width: 100%;
  aspect-ratio: 1;
  border: 1px solid black;
}
.seekerPos {
  background-color: rgb(110, 110, 255);
}
.finishPos {
  background-color: rgb(110, 255, 110);
}
</style>
