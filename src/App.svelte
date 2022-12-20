<script lang="ts">
  import { onMount } from "svelte";
  let IMAGE = {
    width: 0,
    height: 0,
  };
  const RATIO = 1.2;
  $: edge = {
    marginX: IMAGE.width / 4 / RATIO,
    marginY: IMAGE.height / 4,
    width: (IMAGE.width / 2) * RATIO,
    height: IMAGE.height / 2,
  };
  let video: HTMLVideoElement = null;
  let canvas: HTMLCanvasElement = null;
  let imageEdge: HTMLElement = null;

  const getVideoSource = async () => {
    try {
      const stream = await navigator.mediaDevices.getUserMedia({ video: true });
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
      -edge.marginX,
      -edge.marginY,
      IMAGE.width,
      IMAGE.height
    );
  };

  const observer = new ResizeObserver((entries) => {
    for (let entry of entries) {
      const { width, height } = entry.contentRect;
      IMAGE = { width, height };
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
        style="width:{edge.width}px; height:{edge.height}px; margin-left:{edge.marginX}px; margin-top:{edge.marginY}px"
        bind:this={imageEdge}
      />
    </div>
    <button on:click={drawImage}>Capture</button>
  </div>
  <canvas
    id="canvas"
    width={edge.width}
    height={edge.height}
    bind:this={canvas}
  />
</main>

<style>
  #video {
    transform: rotate(0deg);
    position: relative;
    width: 100%;
    max-width: 1000px;
  }
  #videoEdge {
    z-index: 1;
    position: absolute;
    top: 0;
    left: 0;
    border: 2px solid red;
    border-radius: 10px;
  }
  #canvas {
    transform: rotate(0deg);
    position: relative;
  }
</style>
