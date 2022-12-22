<script lang="ts">
  import { onMount } from "svelte";
  const ratio = 0.625;
  let IMAGE = { width: 0, height: 0 };
  $: BOUNDARY_SIZE = IMAGE.width / 1.5;
  $: BOUNDARY = { width: BOUNDARY_SIZE, height: BOUNDARY_SIZE * ratio };
  let offsetX = 0;
  let offsetY = 0;
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
    picture.drawImage(video, -offsetX, -offsetY, IMAGE.width, IMAGE.height);
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
      offsetX = left;
      offsetY = top;
    }
  });

  onMount(async () => {
    await getVideoSource();
    observer.observe(video);
  });
</script>

<main style="max-width: 1000px;">
  <!-- svelte-ignore a11y-media-has-caption -->
  <div style="position: relative;">
    <video autoplay playsinline bind:this={video} id="video" />
    <div
      id="videoEdge"
      style="width:{BOUNDARY.width}px; height:{BOUNDARY.height}px;"
      bind:this={imageEdge}
    />
  </div>
  <div width="100%" id="captureWrapper">
    <button id="captureBtn" on:click={drawImage}>Capture</button>
    <canvas
      width={BOUNDARY.width}
      height={BOUNDARY.height}
      bind:this={canvas}
    />
  </div>
</main>

<style>
  #video {
    position: relative;
    width: 100%;
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
  #captureWrapper {
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  #captureBtn {
    width: 10rem;
    height: 5rem;
    font-size: 1.2rem;
    border-radius: 1rem;
    font-weight: bold;
  }
</style>
