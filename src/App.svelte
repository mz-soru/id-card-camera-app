<script lang="ts">
  import { onMount } from "svelte";
  let IMAGE = { width: 0, height: 0 };
  let BOUNDARY = { left: 0, top: 0 };
  let video: HTMLVideoElement = null;
  let canvas: HTMLCanvasElement = null;
  let imageEdge: HTMLElement = null;

  const getVideoSource = async () => {
    try {
      const stream = await navigator.mediaDevices.getUserMedia({
        video: { facingMode: "environment" },
      });
      video.srcObject = stream;
      video.play();
    } catch {
      alert("에러가 발생했습니다.");
    }
  };

  const drawImage = () => {
    const picture = canvas.getContext("2d");
    picture.clearRect(0, 0, IMAGE.width, IMAGE.height);
    picture.drawImage(
      video,
      -BOUNDARY.left,
      -BOUNDARY.top,
      IMAGE.width,
      IMAGE.height
    );
  };

  const getOffset = (el: HTMLElement) => {
    const rect = el.getBoundingClientRect();
    return { left: rect.left + window.scrollX, top: rect.top + window.scrollY };
  };
  const observer = new ResizeObserver((entries) => {
    for (let entry of entries) {
      const { width, height } = entry.contentRect;
      const { left, top } = getOffset(imageEdge);
      IMAGE = { width, height };
      BOUNDARY = { left, top };
    }
  });

  onMount(async () => {
    await getVideoSource();
    observer.observe(video);
  });
</script>

<main>
  <div style="position:relative; ">
    <!-- svelte-ignore a11y-media-has-caption -->
    <div style="position: relative;">
      <video autoplay playsinline bind:this={video} id="video" />
      <div
        id="videoEdge"
        style="width:{IMAGE.width / 2.5}px; height:{IMAGE.width / 4}px;"
        bind:this={imageEdge}
      />
    </div>
    <button on:click={drawImage}>Capture</button>
  </div>
  <canvas
    id="canvas"
    width={IMAGE.width / 2.5}
    height={IMAGE.width / 4}
    bind:this={canvas}
  />
</main>

<style>
  #video {
    position: relative;
    width: 100%;
    max-width: 1000px;
  }
  #videoEdge {
    z-index: 1;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    border: 2px solid red;
    border-radius: 10px;
  }
  #canvas {
    position: relative;
  }
</style>
