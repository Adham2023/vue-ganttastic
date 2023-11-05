<template>
  <div
    :id="barConfig.id"
    :class="['g-gantt-bar', barConfig.class || '']"
    :style="{
      ...barConfig.style,
      position: 'absolute',
      top: `${rowHeight * 0.1}px`,
      left: `${xStart}px`,
      width: `${xEnd - xStart}px`,
      height: `${rowHeight * 0.8}px`,
      zIndex: isDragging ? 3 : 2
    }"
    @mousedown="onMouseEvent"
    @click="onMouseEvent"
    @dblclick="onMouseEvent"
    @mouseenter="onMouseEvent"
    @mouseleave="onMouseEvent"
    @contextmenu="onMouseEvent"
  >
    <div class="g-gantt-bar-label">
      <slot :bar="bar">
        <div class="g-gantt-bar-label--flight_routes">
          <div v-for="(rt, index) in barConfig.flight_routes" :key="rt" class="bar-tag-route">
            {{ rt }} <span v-if="index < barConfig.flight_routes.length - 1">&#8594;</span>
          </div>
        </div>
        <div class="g-gantt-bar-label--crew_members">
          <div v-for="cr in barConfig.crew_members" :key="cr._id" class="bar-tag-route">
            {{ cr.first_name }}
          </div>
          <!-- {{ barConfig.crew_members.map((cr: any) => cr.first_name).join(",") || "" }} -->
        </div>
      </slot>
    </div>
    <template v-if="barConfig.hasHandles">
      <div class="g-gantt-bar-handle-left" />
      <div class="g-gantt-bar-handle-right" />
    </template>
  </div>
</template>

<script setup lang="ts">
import { computed, ref, toRefs, watch, onMounted, inject } from "vue"

import useBarDragManagement from "../composables/useBarDragManagement.js"
import useTimePositionMapping from "../composables/useTimePositionMapping.js"
import useBarDragLimit from "../composables/useBarDragLimit.js"
import type { GanttBarObject } from "../types"
import provideEmitBarEvent from "../provider/provideEmitBarEvent.js"
import provideConfig from "../provider/provideConfig.js"
import { BAR_CONTAINER_KEY } from "../provider/symbols"

const props = defineProps<{
  bar: GanttBarObject
}>()
const emitBarEvent = provideEmitBarEvent()
const config = provideConfig()
const { rowHeight } = config

const { bar } = toRefs(props)
const { mapTimeToPosition, mapPositionToTime } = useTimePositionMapping()
const { initDragOfBar, initDragOfBundle } = useBarDragManagement()
const { setDragLimitsOfGanttBar } = useBarDragLimit()

const isDragging = ref(false)

const barConfig = computed(() => bar.value.ganttBarConfig)

function firstMousemoveCallback(e: MouseEvent) {
  barConfig.value.bundle != null ? initDragOfBundle(bar.value, e) : initDragOfBar(bar.value, e)
  isDragging.value = true
}

const prepareForDrag = () => {
  setDragLimitsOfGanttBar(bar.value)
  if (barConfig.value.immobile) {
    return
  }

  window.addEventListener("mousemove", firstMousemoveCallback, {
    once: true
  }) // on first mousemove event
  window.addEventListener(
    "mouseup",
    () => {
      // in case user does not move the mouse after mousedown at all
      window.removeEventListener("mousemove", firstMousemoveCallback)
      isDragging.value = false
    },
    { once: true }
  )
}

const barContainerEl = inject(BAR_CONTAINER_KEY)

const onMouseEvent = (e: MouseEvent) => {
  e.preventDefault()
  if (e.type === "mousedown") {
    prepareForDrag()
  }
  const barContainer = barContainerEl?.value?.getBoundingClientRect()
  if (!barContainer) {
    return
  }
  const datetime = mapPositionToTime(e.clientX - barContainer.left)
  emitBarEvent(e, bar.value, datetime)
}

const { barStart, barEnd, width, chartStart, chartEnd, chartSize } = config

const xStart = ref(0)
const xEnd = ref(0)

onMounted(() => {
  watch(
    [bar, width, chartStart, chartEnd, chartSize.width],
    () => {
      xStart.value = mapTimeToPosition(bar.value[barStart.value])
      xEnd.value = mapTimeToPosition(bar.value[barEnd.value])
    },
    { deep: true, immediate: true }
  )
})
</script>

<style>
.bar-tag-route {
  margin-right: 5px;
  padding: 0 3px 0 3px;
  display: block;
  border-radius: 10px;
  /* background: #000; */
  /* border: 1px solid lightgray; */
}

.g-gantt-bar-label--flight_routes,
.g-gantt-bar-label--crew_members {
  padding: 3px 0;
  border-bottom: 1px solid rgb(218, 217, 217);
  display: flex;
}

.g-gantt-bar-label--crew_members {
  border-bottom: none;
}

.g-gantt-bar {
  display: flex;
  justify-content: center;
  align-items: center;
  /* background: cadetblue; */
  /* border: 1px solid gray; */
  overflow: hidden;
}

.g-gantt-bar-label {
  width: 100%;
  height: 100%;
  box-sizing: border-box;
  padding: 0 10px 0 10px; /* 14px is the width of the handle */
  display: flex;
  flex-direction: column;
  align-items: stretch;
  justify-content: center;
  align-items: center;
}
.g-gantt-bar-label > * {
  width: 100%;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
.g-gantt-bar-handle-left,
.g-gantt-bar-handle-right {
  position: absolute;
  width: 10px;
  height: 100%;
  background: white;
  opacity: 0.7;
  border-radius: 0px;
  cursor: ew-resize;
  top: 0;
}
.g-gantt-bar-handle-left {
  left: 0;
}
.g-gantt-bar-handle-right {
  right: 0;
}

.g-gantt-bar-label img {
  pointer-events: none;
}
</style>
