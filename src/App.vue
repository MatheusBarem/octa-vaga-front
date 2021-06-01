<template>
  <div class="container">
    <div class="toolbox">
      <h1>Toolbox</h1>
       <!-- listagem de componentes na toolbox -->
      <div v-for="(item, index) in formComponents" :key="index">
        <div
          @drag="drag($event, item)"
          @dragend="dragend($event, item)"
          class="droppable-element"
          :class="{ dashed: index === 0 || index === 1 }"
          draggable="true"
          unselectable="on"
        >
          <div v-if="item.value == 1">
            <InputToolbox />
          </div>
          <div v-else-if="item.value == 2">
            <CheckboxToolbox />
          </div>
          <div v-else-if="item.value == 3">
            <ButtonToolbox />
          </div>
        </div>
      </div>
    </div>
    <div class="design-area">
      <div id="content">
        <grid-layout
          ref="gridlayout"
          :layout.sync="layout"
          :col-num="12"
          :row-height="30"
          :is-draggable="true"
          :is-resizable="true"
          :vertical-compact="true"
          :responsive="true"
          :use-css-transforms="true"
        >
          <grid-item
            :key="item.i"
            v-for="item in layout"
            :x="item.x"
            :y="item.y"
            :w="item.w"
            :h="item.h"
            :i="item.i"
          >
            <component :is="item.c"></component>
            <span class="remove" @click="removeItem(item.i)">x</span>
          </grid-item>
        </grid-layout>
      </div>
    </div>
  </div>
</template>

<script>
import { GridLayout, GridItem } from "vue-grid-layout";
import InputToolbox from "./components/InputToolbox.vue";
import ButtonToolbox from "./components/ButtonToolbox.vue";
import CheckboxToolbox from "./components/CheckboxToolbox.vue";

let mouseXY = { x: null, y: null };
let DragPos = { x: null, y: null, w: 3, h: 2, i: null, c: null };

export default {
  components: {
    GridLayout,
    GridItem,
    InputToolbox,
    CheckboxToolbox,
    ButtonToolbox,
  },
  data() {
    return {
      // aloja as posições dos componentes na design area utilizando grid css
      layout: [
        { x: 0, y: 0, w: 3, h: 2, i: "0", c: "InputToolbox" },
        { x: 0, y: 5, w: 3, h: 3, i: "6", c: "CheckboxToolbox" },
        { x: 0, y: 5, w: 3, h: 2, i: "6", c: "ButtonToolbox" },
      ],
      // nomes dos componentes para passar o nome do componente para renderização na design area em formato de grid
      formComponents: [
        {
          name: "InputToolbox",
          value: 1,
        },
        {
          name: "CheckboxToolbox",
          value: 2,
        },
        {
          name: "ButtonToolbox",
          value: 3,
        },
      ],
    };
  },
  mounted() {
    document.addEventListener(
      "dragover",
      function (e) {
        mouseXY.x = e.clientX;
        mouseXY.y = e.clientY;
      },
      false
    );
  },
  beforeDestroy() {},
  methods: {
    drag(e, item) {
      let nameComponent = item.name;
      let parentRect = document
        .getElementById("content")
        .getBoundingClientRect();
      let mouseInGrid = false;
      if (
        mouseXY.x > parentRect.left &&
        mouseXY.x < parentRect.right &&
        mouseXY.y > parentRect.top &&
        mouseXY.y < parentRect.bottom
      ) {
        mouseInGrid = true;
      }
      if (
        mouseInGrid === true &&
        this.layout.findIndex((item) => item.i === "drop") === -1
      ) {
        this.layout.push({
          x: (this.layout.length * 2) % (this.colNum || 12),
          y: this.layout.length + (this.colNum || 12),
          w: 3,
          h: 2,
          i: "drop",
          c: nameComponent,
        });
      }
      let index = this.layout.findIndex((item) => item.i === "drop");
      if (index !== -1) {
        try {
          this.$refs.gridlayout.$children[
            this.layout.length
          ].$refs.item.style.display = "none";
        } catch {
          /** */
        }
        let el = this.$refs.gridlayout.$children[index];
        el.dragging = {
          top: mouseXY.y - parentRect.top,
          left: mouseXY.x - parentRect.left,
        };
        let new_pos = el.calcXY(
          mouseXY.y - parentRect.top,
          mouseXY.x - parentRect.left
        );

        if (mouseInGrid === true) {
          this.$refs.gridlayout.dragEvent(
            "dragstart",
            "drop",
            new_pos.x,
            new_pos.y,
            1,
            1
          );
          DragPos.i = String(index);
          DragPos.x = this.layout[index].x;
          DragPos.y = this.layout[index].y;
        }
        if (mouseInGrid === false) {
          this.$refs.gridlayout.dragEvent(
            "dragend",
            "drop",
            new_pos.x,
            new_pos.y,
            1,
            1
          );
          this.layout = this.layout.filter((obj) => obj.i !== "drop");
        }
      }
    },
    dragend(e, item) {
      let nameComponent = item.name;
      let parentRect = document
        .getElementById("content")
        .getBoundingClientRect();
      let mouseInGrid = false;
      if (
        mouseXY.x > parentRect.left &&
        mouseXY.x < parentRect.right &&
        mouseXY.y > parentRect.top &&
        mouseXY.y < parentRect.bottom
      ) {
        mouseInGrid = true;
      }
      if (mouseInGrid === true) {
        this.$refs.gridlayout.dragEvent(
          "dragend",
          "drop",
          DragPos.x,
          DragPos.y,
          1,
          1
        );
        this.layout = this.layout.filter((obj) => obj.i !== "drop");

        this.layout.push({
          x: DragPos.x,
          y: DragPos.y,
          w: 3,
          h: 2,
          i: DragPos.i,
          c: nameComponent,
        });
        this.$refs.gridlayout.dragEvent(
          "dragend",
          DragPos.i,
          DragPos.x,
          DragPos.y,
          1,
          1
        );
        try {
          this.$refs.gridlayout.$children[
            this.layout.length
          ].$refs.item.style.display = "block";
        } catch {
          /* */
        }
      }
    },
    removeItem(val) {
      const index = this.layout.map((item) => item.i).indexOf(val);
      this.layout.splice(index, 1);
    },
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Roboto:wght@300&display=swap");

html,
body {
  padding: 0;
  margin: 0;
  vertical-align: baseline;
  list-style: none;
  border: 0;
  height: 100%;
  font-family: "Roboto", sans-serif;
}

h1,
h2,
h3,
p {
  margin: 0;
}

.container {
  min-height: 100%;
  height: 100%;
}

.toolbox {
  background-color: #ededed;
}

.toolbox h1 {
  font-size: 18px;
  margin: 20px 0;
  padding: 20px;
}

.design-area {
  background-color: #ffffff;
}

.design-area {
  padding: 30px;
}
.droppable-element {
  margin: 20px 0;
  padding: 20px;
}

.dashed {
  border-bottom: 1px dashed #b7b7b7;
}

.remove {
  position: absolute;
  right: 2px;
  top: 0;
  cursor: pointer;
}

.vue-grid-layout {
  background: #ffff;
  border: 1px dashed black;
}

.container .vue-grid-item.vue-grid-placeholder {
  background: #afadad;
}

.vue-grid-item:not(.vue-grid-placeholder) {
  border: 1px dashed #b2b2b2;
}

.vue-grid-item .resizing {
  opacity: 0.9;
}

.vue-grid-item .static {
  background: #cce;
}

.vue-grid-item .text {
  font-size: 24px;
  text-align: center;
  position: absolute;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  margin: auto;
  height: 100%;
  width: 100%;
}

.vue-grid-item .no-drag {
  height: 100%;
  width: 100%;
}

.vue-grid-item .minMax {
  font-size: 12px;
}

.vue-grid-item .add {
  cursor: pointer;
}

.vue-draggable-handle {
  position: absolute;
  width: 20px;
  height: 20px;
  top: 0;
  left: 0;
  background: url("data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='10' height='10'><circle cx='5' cy='5' r='5' fill='#999999'/></svg>")
    no-repeat;
  background-position: bottom right;
  padding: 0 8px 8px 0;
  background-repeat: no-repeat;
  background-origin: content-box;
  box-sizing: border-box;
  cursor: pointer;
}

.layoutJSON {
  background: #ddd;
  border: 1px solid black;
  margin-top: 10px;
  padding: 10px;
}

.layoutJSON {
  background: #ddd;
  border: 1px solid black;
  margin-top: 10px;
  padding: 10px;
}

.columns {
  -moz-columns: 120px;
  -webkit-columns: 120px;
  columns: 120px;
}

/* media queries */
@media (min-width: 768px) {
  .container {
    display: flex;
    min-height: 100%;
    height: 100%;
  }

  .toolbox {
    flex: 3;
    background-color: #ededed;
  }

  .design-area {
    flex: 9;
    background-color: #ffffff;
  }
}
</style>
