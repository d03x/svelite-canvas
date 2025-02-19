<script lang="ts">
  import { preventDefault } from "svelte/legacy";

  let { color, size } = $props();

  let canvas: any = $state();
  let context: any = $state();
  let coords: any = $state();

  $effect(() => {
    context = canvas?.getContext("2d");

    const resize = () => {
      canvas.width = window.innerWidth;
      canvas.height = window.innerHeight;
    };
    window.addEventListener("resize", resize);
    return () => {
      window.removeEventListener("resize", resize);
    };
  });
</script>

<canvas
  bind:this={canvas}
  onpointerdown={(e) => {
    coords = { x: e.offsetX, y: e.offsetY };
    context.fillStyle = color;
    context.beginPath();
    context.arc(coords.x, coords.y, size / 2, 0, 2 * Math.PI);
    context.fill();
  }}
  onpointerleave={() => {
    coords = null;
  }}
  onpointermove={(e) => {
    const prev = coords;
    coords = {
      x: e.offsetX,
      y: e.offsetY,
    };
    if (e.buttons == 1) {
      e.preventDefault();
      context.strokeStyle = color;
      context.lineWidth = size;
      context.lineCap = "round";
      context.beginPath();
      context.moveTo(prev.x, prev.y);
      context.lineTo(coords.x, coords.y);
      context.stroke();
    }
  }}
>
</canvas>
{#if coords}
  <div
    class="preview"
    style="--color: {color}; --size: {size}px; --x: {coords.x}px; --y: {coords.y}px"
  ></div>
{/if}

<style>
  * {
    padding: 0;
    margin: 0;
  }
  canvas {
    border: 1px solid;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
  }
  /* .preview {
    position: absolute;
    left: var(--x);
    top: var(--y);
    width: var(--size);
    height: var(--size);
    transform: translate(-50%, -50%);
    background: var(--color);
    border-radius: 50%;
    opacity: 0.5;
    pointer-events: none;
  } */
</style>
