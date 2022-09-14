<template>
  <canvas
    width="500"
    height="500"
    id="canvas"
    @mousedown="startDrawing"
    @mouseup="stopDrawing"
    @mousemove="Draw"
  ></canvas>

  <h1 style="text-align: center">JoyStick Component</h1>
  <h2 style="text-align: center">status: {{ status }}</h2>
</template>

<script>
import { onMounted, ref } from "@vue/runtime-core";
export default {
  setup() {
    const coords = ref({ x: 0, y: 0 }); //canvas 上的座標
    const paint = ref(false);
    const radius = 100;
    const status = ref({
      x_coordinate: 0,
      y_coordinate: 0,
      speed: 0,
      angle: 0,
    });

    var canvas, x_orig, y_orig, ctx, width, height;

    function getPosition(event) {
      var mouse_x = event.clientX || event.touches[0].clientX;
      var mouse_y = event.clientY || event.touches[0].clientY;
      coords.value.x = mouse_x - canvas.offsetLeft;
      coords.value.y = mouse_y - canvas.offsetTop;
      //   console.log(mouse_x, mouse_y);
    }

    function is_it_in_the_circle() {
      var current_radius = Math.sqrt(
        Math.pow(coords.value.x - 0, 2) + Math.pow(coords.value.y - 0, 2)
      );
      if (radius >= current_radius) return true;
      else return false;
    }
    const background = () => {
      x_orig = width / 2;
      y_orig = height / 2;

      ctx.beginPath();
      ctx.arc(x_orig, y_orig, radius + 50, 0, Math.PI * 2, true);
      ctx.fillStyle = "#35495e";
      ctx.fill();
    };
    const joystick = (x, y) => {
      ctx.beginPath();
      ctx.arc(x + x_orig, y + y_orig, radius, 0, Math.PI * 2, true);
      ctx.fillStyle = "#42b883";
      ctx.fill();
      ctx.strokeStyle = "#FFFFFF";
      ctx.lineWidth = 8;
      ctx.stroke();
    };
    function startDrawing(event) {
      paint.value = true;
      getPosition(event);
      console.log("is_it_in_the_circle()", is_it_in_the_circle());
      if (is_it_in_the_circle()) {
        console.log("inside");
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        background();
        joystick(coords.value.x, coords.value.y);
        // Draw();
      }
    }

    function stopDrawing() {
      paint.value = false;
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      background();
      joystick(0, 0);
      status.value.x_coordinate = 0;
      status.value.y_coordinate = 0;
      status.value.speed = 0;
      status.value.angle = 0;
    }

    function Draw(event) {
      if (paint.value) {
        console.log("draw");
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        background();
        var angle_in_degrees, x, y, speed;
        var angle = Math.atan2(coords.value.y, coords.value.x);

        if (Math.sign(angle) == -1) {
          angle_in_degrees = Math.round((-angle * 180) / Math.PI);
        } else {
          angle_in_degrees = Math.round(360 - (angle * 180) / Math.PI);
        }

        if (is_it_in_the_circle()) {
          //   console.log("in draw in circle");
          joystick(coords.value.x, coords.value.y);
          x = coords.value.x;
          y = coords.value.y;
        } else {
          //   console.log("in draw isn't circle");
          x = radius * Math.cos(angle);
          y = radius * Math.sin(angle);
          joystick(x, y);
        }

        getPosition(event);

        speed = Math.round(
          (100 * Math.sqrt(Math.pow(x, 2) + Math.pow(y, 2))) / radius
        );

        var x_relative = Math.round(x);
        var y_relative = -Math.round(y);

        status.value.x_coordinate = x_relative;
        status.value.y_coordinate = y_relative;
        status.value.speed = speed;
        status.value.angle = angle_in_degrees;
      }
    }
    onMounted(() => {
      const dom = document.getElementById("canvas");
      const resize = () => {
        width = dom.getBoundingClientRect().width;
        height = dom.getBoundingClientRect().height;
        ctx.canvas.width = width;
        ctx.canvas.height = height;
        background(width, height, radius);
        joystick(0, 0);
      };

      canvas = document.getElementById("canvas");
      ctx = canvas.getContext("2d");
      resize();
      window.addEventListener("resize", resize);
    });
    return {
      coords,
      paint,
      startDrawing,
      stopDrawing,
      Draw,
      status,
    };
  },
};
</script>

<style>
#canvas {
  position: absolute;
  -ms-transform: translate(-50%, -50%);
  transform: translate(-50%, -50%);
  top: 50%;
  left: 50%;

  /* background: rgb(104, 165, 255); */
}
</style>
