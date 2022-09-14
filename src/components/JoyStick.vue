<template>
  <div class="container">
    <h1>{{ coordinates }}</h1>
    <div class="outsider" id="outsider">
      <div
        class="insider"
        id="insider"
        draggable="true"
        @dragstart="onDrag"
        @drag="move"
        @dragend="release"
      ></div>
    </div>
  </div>
</template>

<script>
import { ref } from "@vue/reactivity";
import { onMounted } from "@vue/runtime-core";
export default {
  setup() {
    const coordinates = ref({
      x: 0,
      y: 0,
      acceleration: 0,
    });

    const firstInsiderCoordinates = ref({
      x: 0,
      y: 0,
    });
    const first = () => {
      console.log("first");

      let outsider = document.getElementById("outsider");
      let rectOutside = outsider.getBoundingClientRect();
      firstInsiderCoordinates.value.x = rectOutside.width / 2;
      firstInsiderCoordinates.value.y = rectOutside.height / 2;
    };

    const onDrag = (event) => {
      let el = event.target;
      el.classList.add("hide");
    };

    const move = (event) => {
      let outsider = document.getElementById("outsider");
      let insider = document.getElementById("insider");

      let rectInside = insider.getBoundingClientRect();
      let rectOutside = outsider.getBoundingClientRect();
      let insiderCoordinates = {
        x: 0,
        y: 0,
      };
      insiderCoordinates.x = rectInside.x + rectInside.width / 2;
      insiderCoordinates.y = rectInside.y + rectInside.height / 2;

      // insider.style.left = event.clientX - 700 + "px";
      // insider.style.top = event.clientY - 780 + "px";
      let adjX = event.clientX - (rectInside.x + rectInside.width / 2);
      let adjY =
        event.clientY - firstInsiderCoordinates.value.y - rectOutside.y;
      insider.style.left = event.clientX - rectOutside.x - adjX + "px";
      insider.style.top = event.clientY - rectOutside.y + "px";

      let outsiderCoordinates = {
        x: 0,
        y: 0,
      };

      outsiderCoordinates.x = rectOutside.x + rectOutside.width / 2;
      outsiderCoordinates.y = rectOutside.y + rectOutside.height / 2;

      coordinates.value.x = insiderCoordinates.x - outsiderCoordinates.x;
      coordinates.value.y = outsiderCoordinates.y - insiderCoordinates.y;
      coordinates.value.acceleration = Math.sqrt(
        Math.pow(coordinates.value.x, 2) + Math.pow(coordinates.value.y, 2)
      );

      // console.log("outsiderCoordinates", outsiderCoordinates);
      // console.log("insiderCoordinates", insiderCoordinates);
      console.log(adjX, adjY);
    };
    const release = () => {
      let insider = document.getElementById("insider");
      insider.style.left = firstInsiderCoordinates.value.x + "px";
      insider.style.top = firstInsiderCoordinates.value.y + "px";
      coordinates.value.x = 0;
      coordinates.value.y = 0;
      coordinates.value.acceleration = 0;
    };

    onMounted(() => {
      first();
    });
    return {
      move,
      coordinates,
      release,
      onDrag,
    };
  },
};
</script>

<style>
.container {
  margin: auto;
}
.outsider {
  margin: 100px auto;
  position: relative;
  width: 400px;
  height: 400px;
  background: rgb(0, 41, 98);
  border-radius: 50%;
}
.insider {
  position: absolute;
  top: 50%;
  left: 50%;
  -ms-transform: translate(-50%, -50%);
  transform: translate(-50%, -50%);
  width: 300px;
  height: 300px;
  background: rgb(255, 255, 0);
  border-radius: 50%;
}
</style>
