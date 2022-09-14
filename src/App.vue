<template>
  <div id="app">
    <h1 style="text-align: center">Vue Grid Layout</h1>
    <div id="content">
      <grid-layout
        id="grid-layout"
        class="grid"
        :style="`--cols: ${colNum}; --row-height: ${rowHeight};`"
        :margin="[parseInt(marginX), parseInt(marginY)]"
        :layout.sync="layout"
        :col-num="colNum"
        :row-height="rowHeight"
        :is-draggable="draggable"
        :is-resizable="resizable"
        :is-mirrored="mirrored"
        :is-bounded="bounded"
        :prevent-collision="preventCollision"
        :vertical-compact="compact"
        :restore-on-drag="restoreOnDrag"
        :use-css-transforms="true"
        :responsive="responsive"
        :transformScale="transformScale"
        @layout-created="layoutCreatedEvent"
        @layout-before-mount="layoutBeforeMountEvent"
        @layout-mounted="layoutMountedEvent"
        @layout-ready="layoutReadyEvent"
        @layout-updated="layoutUpdatedEvent"
        @breakpoint-changed="breakpointChangedEvent"
      >
        <grid-item
          v-for="item in layout"
          :key="item.i"
          :static="item.static"
          :x="item.x"
          :y="item.y"
          :w="item.w"
          :h="item.h"
          :i="item.i"
          :min-w="item.minW"
          :max-w="item.maxW"
          :min-x="item.minX"
          :max-x="item.maxX"
          :min-y="item.minY"
          :max-y="item.maxY"
          :preserve-aspect-ratio="item.preserveAspectRatio"
          @resize="resize"
          @move="move"
          @resized="resized"
          @container-resized="containerResized"
          @moved="moved"
        >
          <span style="display: flex; margin: 20px">{{ item.h }}</span>
          <!-- <custom-drag-element :text="item.i"></custom-drag-element> -->
          <!-- <test-element :text="item.i" @removeItem="removeItem($event)"></test-element> -->
        </grid-item>
      </grid-layout>
    </div>
  </div>
</template>

<script>
import GridItem from "./components/GridItem.vue";
import GridLayout from "./components/GridLayout.vue";
import TestElement from "./components/TestElement.vue";
import CustomDragElement from "./components/CustomDragElement.vue";
import { getDocumentDir, setDocumentDir } from "./helpers/DOM";
const g = (h) => Math.ceil(h / (17 / 6));
let testLayout = [
  //   { x: 0, y: 10, w: 1, h: g(2.2) },
  //   { x: 1, y: 10, w: 1, h: g(2.2) },
  //   { x: 2, y: 10, w: 1, h: g(2.2) },
  //   { x: 3, y: 10, w: 1, h: g(2.2) },
  //   { x: 4, y: 10, w: 1, h: g(2.2) },
  //   { x: 5, y: 10, w: 1, h: g(2.2) },
  //   { x: 0, y: 15, w: 1, h: g(4) },
  //   { x: 1, y: 15, w: 1, h: g(4) },
  //   { x: 2, y: 15, w: 1, h: g(4) },
  //   { x: 3, y: 15, w: 1, h: g(4) },
  //   { x: 4, y: 15, w: 1, h: g(4) },
  //   { x: 5, y: 15, w: 1, h: g(4) },
  //   { x: 0, y: 20, w: 1, h: g(27.9) },
  { x: 1, y: 20, w: 1, h: g(27.9) },
  { x: 2, y: 20, w: 1, h: g(27.9) },
  { x: 3, y: 20, w: 1, h: g(27.9) },
  { x: 4, y: 20, w: 1, h: g(27.9) },
  { x: 5, y: 20, w: 1, h: g(27.9) },
  //   { x: 0, y: 40, w: 1, h: g(47.4) },
  { x: 1, y: 40, w: 1, h: g(47.4) },
  { x: 2, y: 40, w: 1, h: g(47.4) },
  { x: 3, y: 40, w: 1, h: g(47.4) },
  { x: 4, y: 40, w: 1, h: g(47.4) },
  { x: 5, y: 40, w: 1, h: g(47.4) },
  //   { x: 0, y: 60, w: 1, h: g(103) },
  { x: 1, y: 60, w: 1, h: g(103) },
  { x: 2, y: 60, w: 1, h: g(103) },
  { x: 3, y: 60, w: 1, h: g(103) },
  { x: 4, y: 60, w: 1, h: g(103) },
  { x: 5, y: 60, w: 1, h: g(103) },
];
testLayout = testLayout.map((item, index) => {
  item.i = index.toString();
  return item;
});

export default {
  name: "app",
  components: {
    GridLayout,
    GridItem,
    TestElement,
    CustomDragElement,
  },
  data() {
    return {
      layout: JSON.parse(JSON.stringify(testLayout)),
      draggable: true,
      resizable: false,
      mirrored: false,
      responsive: false,
      bounded: false,
      transformScale: 1,
      preventCollision: true,
      compact: false,
      restoreOnDrag: true,
      rowHeight: 17 / 6,
      colNum: 7,
      index: 0,
      marginX: 10,
      marginY: 10,
    };
  },
  mounted: function () {
    this.index = this.layout.length;
    this.$nextTick(() => {
    //   this.colNum =
    //     this.layout.map((item) => item.x).reduce((a, b) => Math.max(a, b)) + 2;
    //   this.eventBus.$emit("setColNum", this.colNum);
});
setTimeout(() => {
this.extendGrid(this.layout);
}, 2000)
  },
  methods: {
    increaseWidth: function () {
      let width = document.getElementById("content").offsetWidth;
      width += 20;
      document.getElementById("content").style.width = width + "px";
    },
    decreaseWidth: function () {
      let width = document.getElementById("content").offsetWidth;
      width -= 20;
      document.getElementById("content").style.width = width + "px";
    },
    scaleHalf: function () {
      this.transformScale = 0.5;
      document.getElementById("grid-layout").style.transform = "scale(0.5)";
    },
    scaleThreeQuarters: function () {
      this.transformScale = 0.75;
      document.getElementById("grid-layout").style.transform = "scale(0.75)";
    },
    scaleIdentity: function () {
      this.transformScale = 1;
      document.getElementById("grid-layout").style.transform = "scale(1)";
    },
    removeItem: function (i) {
      console.log("### REMOVE " + i);
      const index = this.layout.map((item) => item.i).indexOf(i);
      this.layout.splice(index, 1);
    },
    addItem: function () {
      // let self = this;
      //console.log("### LENGTH: " + this.layout.length);
      let item = { x: 0, y: 0, w: 2, h: 2, i: this.index + "", whatever: "bbb" };
      this.index++;
      this.layout.push(item);
    },
    addItemDynamically: function () {
      const x = (this.layout.length * 2) % (this.colNum || 12);
      const y = this.layout.length + (this.colNum || 12);
      console.log("X=" + x + " Y=" + y);
      let item = {
        x: x,
        y: y,
        w: 2,
        h: 2,
        i: this.index + "",
      };
      this.index++;
      this.layout.push(item);
    },
    move: function (i, newX, newY) {
      console.log("MOVE i=" + i + ", X=" + newX + ", Y=" + newY);
    },
    resize: function (i, newH, newW, newHPx, newWPx) {
      console.log(
        "RESIZE i=" +
          i +
          ", H=" +
          newH +
          ", W=" +
          newW +
          ", H(px)=" +
          newHPx +
          ", W(px)=" +
          newWPx
      );
    },
    moved: function (i, newX, newY) {
      console.log("### MOVED i=" + i + ", X=" + newX + ", Y=" + newY);
    },
    resized: function (i, newH, newW, newHPx, newWPx) {
      console.log(
        "### RESIZED i=" +
          i +
          ", H=" +
          newH +
          ", W=" +
          newW +
          ", H(px)=" +
          newHPx +
          ", W(px)=" +
          newWPx
      );
    },
    containerResized: function (i, newH, newW, newHPx, newWPx) {
      console.log(
        "### CONTAINER RESIZED i=" +
          i +
          ", H=" +
          newH +
          ", W=" +
          newW +
          ", H(px)=" +
          newHPx +
          ", W(px)=" +
          newWPx
      );
    },
    /**
     * Add change direction button
     */
    changeDirection() {
      let documentDirection = getDocumentDir();
      let toggle = "";
      if (documentDirection === "rtl") {
        toggle = "ltr";
      } else {
        toggle = "rtl";
      }
      setDocumentDir(toggle);
      //eventBus.$emit('directionchange');
    },

    layoutCreatedEvent: function (newLayout) {
      console.log("Created layout: ", newLayout);
    },
    layoutBeforeMountEvent: function (newLayout) {
      console.log("beforeMount layout: ", newLayout);
    },
    layoutMountedEvent: function (newLayout) {
      console.log("Mounted layout: ", newLayout);
    },
    layoutReadyEvent: function (newLayout) {
      console.log("Ready layout: ", newLayout);
    },
    layoutUpdatedEvent: function (newLayout) {
      console.log("Updated layout: ", newLayout);
      this.extendGrid(newLayout);
    },
    extendGrid(layout) {
      const columns = layout.map((item) => item.x);
      const leftBound = columns.reduce((a, b) => Math.min(a, b));
      const rightBound = columns.reduce((a, b) => Math.max(a, b)) + 1;
      if (rightBound - leftBound > 10) return;
      this.colNum = rightBound - leftBound + 2;
      this.layout.forEach((item) => (item.x += 1 - leftBound));
    },
    breakpointChangedEvent: function (newBreakpoint, newLayout) {
      console.log(
        "breakpoint changed breakpoint=",
        newBreakpoint,
        ", layout: ",
        newLayout
      );
    },
  },
};
</script>

<style lang="css">
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
</style>
