<template>
  <g-gantt-chart
    :chart-start="chartStart"
    :chart-end="chartEnd"
    precision="month"
    :row-height="60"
    grid
    width="100%"
    bar-start="beginDate"
    bar-end="endDate"
    :date-format="format"
    @click-bar="onClickBar($event.bar, $event.e, $event.datetime)"
    @mousedown-bar="onMousedownBar($event.bar, $event.e, $event.datetime)"
    @dblclick-bar="onMouseupBar($event.bar, $event.e, $event.datetime)"
    @mouseenter-bar="onMouseenterBar($event.bar, $event.e)"
    @mouseleave-bar="onMouseleaveBar($event.bar, $event.e)"
    @dragstart-bar="onDragstartBar($event.bar, $event.e)"
    @drag-bar="onDragBar($event.bar, $event.e)"
    @dragend-bar="onDragendBar($event.bar, $event.e, $event.movedBars)"
    @contextmenu-bar="onContextmenuBar($event.bar, $event.e, $event.datetime)"
  >
    <g-gantt-row label="My row 1" :bars="bars1" highlight-on-hover />
    <g-gantt-row label="My row 2" highlight-on-hover :bars="bars2" />
  </g-gantt-chart>

  <button type="button" @click="addBar()">Add bar</button>
  <button type="button" @click="deleteBar()">Delete bar</button>
</template>

<script setup lang="ts">
import { ref } from "vue"
import type { GanttBarObject } from "./types.js"

const chartStart = ref("21.03.2021 12:00")
const chartEnd = ref("15.07.2021 12:00")
const format = ref("DD.MM.YYYY HH:mm")

const bars1 = ref<GanttBarObject[]>([
  {
    beginDate: "24.04.2021 13:00",
    endDate: "25.05.2021 19:00",
    ganttBarConfig: {
      style: {
        background: "white",
        boxShadow: "rgba(0, 0, 0, 0.05) 0px 6px 24px 0px, rgba(0, 0, 0, 0.08) 0px 0px 0px 1px",
        // color: "white",
        fontSize: "12px",
        borderRadius: "30px"
      },
      flight_routes: ["TAS", "SKD", "TAS"],
      crew_members: [
        {
          _id: "12",
          first_name: "A"
        }
      ],
      id: "8621987329",
      label: "I'm in a bundle",
      bundle: "bundle2"
    }
  }
])

const bars2 = ref<GanttBarObject[]>([
  {
    beginDate: "24.04.2021 13:00",
    endDate: "25.05.2021 19:00",
    ganttBarConfig: {
      flight_routes: ["TAS", "SKD", "TAS"],
      crew_members: [
        {
          _id: "12",
          first_name: "A"
        }
      ],
      id: "1592311887",
      label: "I'm in a bundle",
      bundle: "bundle2",
      style: {
        boxShadow: "rgba(0, 0, 0, 0.05) 0px 6px 24px 0px, rgba(0, 0, 0, 0.08) 0px 0px 0px 1px",
        background: "white",
        // color: "white",
        borderRadius: "30px",
        fontSize: "12px"
      }
    }
  },
  {
    beginDate: "01.01.2021 00:00",
    endDate: "01.03.2021 00:00",
    ganttBarConfig: {
      flight_routes: ["TAS", "SKD", "TAS"],
      crew_members: [
        {
          _id: "12",
          first_name: "A"
        }
      ],
      id: "7716981641",
      label: "Lorem ipsum dolor",
      hasHandles: true,
      style: {
        boxShadow: "rgba(0, 0, 0, 0.05) 0px 6px 24px 0px, rgba(0, 0, 0, 0.08) 0px 0px 0px 1px",
        background: "white",
        // color: "white",
        fontSize: "12px"
      }
    }
  },
  {
    beginDate: "15.06.2021 00:00",
    endDate: "10.07.2021 00:00",
    ganttBarConfig: {
      flight_routes: ["TAS", "SKD", "TAS"],
      crew_members: [
        {
          _id: "12",
          first_name: "A"
        }
      ],
      id: "9716981641",
      label: "Oh hey",
      style: {
        boxShadow: "rgba(0, 0, 0, 0.05) 0px 6px 24px 0px, rgba(0, 0, 0, 0.08) 0px 0px 0px 1px",
        background: "white",
        // color: "white",
        borderRadius: "30px",
        // color: "blue",
        fontSize: "12px"
      }
    }
  }
])
const addBar = () => {
  if (bars1.value.some((bar) => bar.ganttBarConfig.id === "test1")) {
    return
  }
  const bar: GanttBarObject = {
    beginDate: "26.02.2021 00:00",
    endDate: "26.03.2021 02:00",
    ganttBarConfig: {
      id: "test1",
      flight_routes: ["TAS", "SKD", "TAS"],
      crew_members: [
        {
          _id: "12",
          first_name: "A"
        }
      ],
      hasHandles: true,
      label: "Hello!",
      style: {
        boxShadow: "rgba(0, 0, 0, 0.05) 0px 6px 24px 0px, rgba(0, 0, 0, 0.08) 0px 0px 0px 1px",
        // background: "black",
        // color: "white",
        borderRadius: "30px"
      }
    }
  }
  bars1.value.push(bar)
}

const deleteBar = () => {
  const idx = bars1.value.findIndex((b) => b.ganttBarConfig.id === "test1")
  if (idx !== -1) {
    bars1.value.splice(idx, 1)
  }
}

const onClickBar = (bar: GanttBarObject, e: MouseEvent, datetime?: string) => {
  console.log("click-bar", bar, e, datetime)
}

const onMousedownBar = (bar: GanttBarObject, e: MouseEvent, datetime?: string) => {
  console.log("mousedown-bar", bar, e, datetime)
}

const onMouseupBar = (bar: GanttBarObject, e: MouseEvent, datetime?: string) => {
  console.log("mouseup-bar", bar, e, datetime)
}

const onMouseenterBar = (bar: GanttBarObject, e: MouseEvent) => {
  console.log("mouseenter-bar", bar, e)
}

const onMouseleaveBar = (bar: GanttBarObject, e: MouseEvent) => {
  console.log("mouseleave-bar", bar, e)
}

const onDragstartBar = (bar: GanttBarObject, e: MouseEvent) => {
  console.log("dragstart-bar", bar, e)
}

const onDragBar = (bar: GanttBarObject, e: MouseEvent) => {
  console.log("drag-bar", bar, e)
}

const onDragendBar = (
  bar: GanttBarObject,
  e: MouseEvent,
  movedBars?: Map<GanttBarObject, { oldStart: string; oldEnd: string }>
) => {
  console.log("dragend-bar", bar, e, movedBars)
}

const onContextmenuBar = (bar: GanttBarObject, e: MouseEvent, datetime?: string) => {
  console.log("contextmenu-bar", bar, e, datetime)
}
</script>
