<script setup>
import { ref, computed } from "vue";
import { Point, Line } from "@mathigon/euclid";

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
const yCenterRatio = ref(0.5);
const vCenter = computed(() => {
  return height.value * yCenterRatio.value;
});

// K
const middleRightK = computed(() => {
  return letterWidth.value - strokeWidth.value / 2 + 1123 - width.value;
});
const topK = computed(() => {
  // anchor point 1: top left of image
  const a0 = new Point(0, 0);
  // anchor point 2: middle right of K
  const a1 = new Point(
    // letterWidth.value - strokeWidth.value / 2,
    middleRightK.value,
    vCenter.value
  );
  const dist = new Line(a0, a1).length;
  const internalAngle = Math.asin(strokeWidth.value / dist);
  const a2 = a0.rotate(internalAngle, a1);
  const edge = new Line(a1, a2);
  const shift = edge.perpendicular(a2).unitVector;
  const midLine = edge.shift(
    (shift.x * strokeWidth.value) / 2,
    (shift.y * strokeWidth.value) / 2
  );
  return {
    x1: midLine.p1.x,
    y1: midLine.p1.y,
    x2: midLine.p2.x,
    y2: midLine.p2.y,
  };
});
const bottomK = computed(() => {
  // anchor point 1: bottom left of image
  const a0 = new Point(0, height.value);
  // anchor point 2: middle right of K
  const a1 = new Point(
    // letterWidth.value - strokeWidth.value / 2,
    middleRightK.value,
    vCenter.value
  );
  const dist = new Line(a0, a1).length;
  const internalAngle = Math.asin(strokeWidth.value / dist);
  const a2 = a0.rotate(-internalAngle, a1);
  const edge = new Line(a1, a2);
  const shift = edge.perpendicular(a2).unitVector;
  const midLine = edge.shift(
    (-shift.x * strokeWidth.value) / 2,
    (-shift.y * strokeWidth.value) / 2
  );
  return {
    x1: midLine.p1.x,
    y1: midLine.p1.y,
    x2: midLine.p2.x,
    y2: midLine.p2.y,
  };
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
    <line v-bind="topK" />
    <line v-bind="bottomK" />
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
  <input type="range" v-model.number="width" min="1000" max="1300" />
</template>

<style scoped>
#logo {
  border: 1px dashed black;
  background-color: black;
}
#logo line {
  /* opacity: 0.6; */
  stroke: white;
  stroke-width: v-bind(strokeWidth);
}
</style>
