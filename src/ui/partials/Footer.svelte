<script>
  import { onMount } from "svelte";

  const { ipcRenderer } = require("electron");

  import { footerData } from "../../stores.mjs";

  let progressBarWidth = 0;
  let wait = "";

  onMount(() => {
    footerData.set({
      topText: "UBports Installer is starting up",
      underText: "Starting adb service"
    });
  });

  ipcRenderer.on("user:write:status", (e, status, waitDots) => {
    $footerData.topText = status;
    $footerData.waitingDots = waitDots;
  });

  ipcRenderer.on("user:write:speed", (e, speed) => {
    $footerData.speedText = speed;
  });

  ipcRenderer.on("user:write:progress", (e, length) => {
    if (length >= 100) {
      length = 100;
    }
    progressBarWidth = length.toString() + "%";
  });

  export function resetProgress() {
    progressBarWidth = 0;
  }

  const dots = window.setInterval(function () {
    if (wait.length > 4) wait = "";
    else wait += ".";
  }, 400);
</script>

<div class="progress">
  <div class="progress-bar" style="--progressWidth:{progressBarWidth}" />
</div>
<footer class="footer">
  <div class="container">
    <h3>
      <span id="footer-top">
        {$footerData.topText}
        {#if $footerData.waitingDots}
          {wait}
        {/if}
      </span>
    </h3>
    <p>
      <span id="footer-bottom">
        {$footerData.underText}
      </span>
      {#if $footerData.speedText}
        <span>
          {$footerData.speedText}
        </span>
      {/if}
    </p>
  </div>
</footer>

<style>
  .progress {
    display: flex;
    flex: 1 1 auto;
    flex-direction: row;
    max-height: 4px;
    background-color: #f5f5f5;
    margin: 0;
  }

  .progress-bar {
    width: var(--progressWidth);
    height: 4px;
    background-color: #e95420;
  }

  .footer {
    display: flex;
    flex: 1 1 auto;
    flex-direction: row;
    height: 90px;
    max-height: 90px;
    background-color: #f5f5f5;
    margin: 0;
  }
</style>
