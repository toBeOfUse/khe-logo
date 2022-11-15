<script setup>
import { ref, computed } from "vue";
import { Point, Line, intersections } from "@mathigon/euclid";

const strokeWidth = ref(100);

// basic guidelines
const height = ref(500);
const width = ref(1123);
const viewBox = computed(() => {
  return `0 0 ${width.value} ${height.value}`;
});
const letterWidth = computed(() => {
  return (width.value + strokeWidth.value * 2) / 3;
});
const vCenter = computed(() => {
  return height.value / 2;
});

// K
const kPoly = computed(() => {
  const a0 = new Point(0, 0);
  const a1 = new Point(0, height.value);
  const l0 = new Line(a0, a0.shift(1, 1));
  const l1 = new Line(a1, a1.shift(1, -1));
  const midLeft = intersections(l0, l1)[0];
  const frame = [a0, midLeft, a1];
  const offset = strokeWidth.value / Math.cos(Math.PI / 4);
  return frame
    .concat([...frame].reverse().map((p) => p.shift(offset, 0)))
    .map((p) => `${p.x},${p.y}`)
    .join(" ");
});

// H
const leftBar = computed(() => {
  return letterWidth.value - strokeWidth.value / 2;
});
const rightBar = computed(() => {
  return width.value - letterWidth.value + strokeWidth.value / 2;
});

// E
const eLeft = computed(() => {
  return width.value - letterWidth.value;
});
const middleERatio = ref(0.9);
const middleEWidth = computed(() => {
  return letterWidth.value * middleERatio.value;
});
</script>

<template>
  <svg id="logo" :width="width" :height="height" :viewBox="viewBox">
    <polygon :points="kPoly" />
    <line :x1="leftBar" y1="0" :x2="leftBar" :y2="height" />
    <line :x1="leftBar" :y1="vCenter" :x2="rightBar" :y2="vCenter" />
    <line :x1="rightBar" y1="0" :x2="rightBar" :y2="height" />
    <line :x1="eLeft" :y1="strokeWidth / 2" :x2="width" :y2="strokeWidth / 2" />
    <line
      :x1="eLeft"
      :y1="height / 2"
      :x2="eLeft + middleEWidth"
      :y2="height / 2"
    />
    <line
      :x1="eLeft"
      :y1="height - strokeWidth / 2"
      :x2="width"
      :y2="height - strokeWidth / 2"
    />
  </svg>
  <div id="controlRow">
    <div class="control">
      <p>Width</p>
      <input type="range" v-model.number="width" min="1000" max="1300" />
      <p>{{ width }}</p>
    </div>
    <div class="control">
      <p>Stroke Width</p>
      <input type="range" v-model.number="strokeWidth" min="50" max="150" />
      <p>{{ strokeWidth }}</p>
    </div>
  </div>
</template>

<style scoped>
#logo {
  border: 1px dashed black;
  background-color: black;
  max-width: 95vw;
  height: auto;
}
#logo line {
  stroke: white;
  stroke-width: v-bind(strokeWidth);
}
#logo polygon {
  fill: white;
}
#controlRow {
  display: flex;
  flex-direction: row;
  justify-content: space-evenly;
  margin: 5px;
  width: 100%;
}
.control {
  display: flex;
  flex-direction: column;
  place-items: center;
  padding: 5px;
}
</style>
