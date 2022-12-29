<script lang="ts">
  import { onMount } from "svelte";
  let video: HTMLVideoElement = null;
  let canvas: HTMLCanvasElement = null;
  let imageEdge: HTMLElement = null;
  const ratio = 5 / 8;
  $: imageWidth = video?.offsetWidth / 1.5;
  $: imageHeight = (video?.offsetWidth / 1.5) * ratio;

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

  const drawImageToCanvas = () => {
    const picture = canvas.getContext("2d");
    const { left, top } = imageEdge.getBoundingClientRect();
    picture.clearRect(0, 0, video.offsetWidth, video.offsetHeight);
    picture.drawImage(
      video,
      -left,
      -top,
      video.offsetWidth,
      video.offsetHeight
    );
  };

  onMount(async () => {
    await getVideoSource();
  });
</script>

<main style="max-width: 1000px;">
  <!-- svelte-ignore a11y-media-has-caption -->
  <div style="position: relative;">
    <video autoplay playsinline bind:this={video} id="video" />
    <div
      id="videoEdge"
      style="width:{imageWidth}px; height:{imageHeight}px;"
      bind:this={imageEdge}
    />
  </div>
  <div id="captureWrapper">
    <button id="captureBtn" on:click={drawImageToCanvas}>Capture</button>
    <canvas width={imageWidth} height={imageHeight} bind:this={canvas} />
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
