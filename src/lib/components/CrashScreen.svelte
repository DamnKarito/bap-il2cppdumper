<script lang="ts">
  import lottie from "lottie-web";
  import { onMount } from "svelte";
  import { t } from "$lib/stores";

  let { crashLog, onrestart }: { crashLog: string; onrestart: () => void } = $props();

  let lottieContainer: HTMLDivElement;
  let headerVisible = $state(false);
  let detailsVisible = $state(false);
  let actionsVisible = $state(false);

  let crashLines = $derived(crashLog.split("\n"));
  let exceptionLine = $derived(crashLines.find(l => l.includes("panicked") || l.includes("Error") || l.includes("Exception") || l.includes("Panic")));
  let threadLine = $derived(crashLines.find(l => l.startsWith("Thread:") || l.startsWith("thread")));

  function copyLog() {
    navigator.clipboard.writeText(crashLog);
  }

  onMount(() => {
    lottie.loadAnimation({
      container: lottieContainer,
      path: "/error.json",
      loop: false,
      autoplay: true,
      rendererSettings: { preserveAspectRatio: "xMidYMid slice" },
    });

    setTimeout(() => headerVisible = true, 100);
    setTimeout(() => detailsVisible = true, 250);
    setTimeout(() => actionsVisible = true, 400);
  });
</script>

<div class="flex flex-col h-full overflow-y-auto p-4 gap-4 animate-slide-up">
  <!-- Crash Header -->
  <div
    class="transition-all duration-400 ease-out"
    style:opacity={headerVisible ? 1 : 0}
    style:transform="translateY({headerVisible ? 0 : 20}px)"
  >
    <div class="app-card p-8 text-center space-y-4">
      <div class="size-24 mx-auto rounded-2xl flex items-center justify-center" style="background: var(--error-soft); box-shadow: inset 0 0 0 1px var(--error-ring);">
        <div bind:this={lottieContainer} class="size-20"></div>
      </div>
      <h2 class="text-xl font-bold" style="color: var(--error);">Application Crashed</h2>
      <p class="text-sm max-w-xs mx-auto" style="color: var(--text-secondary);">
        An unexpected error occurred. You can copy the crash report and restart.
      </p>
    </div>
  </div>

  <!-- Exception / Thread Info -->
  {#if exceptionLine || threadLine}
    <div
      class="transition-all duration-400 ease-out"
      style:opacity={detailsVisible ? 1 : 0}
      style:transform="translateY({detailsVisible ? 0 : 15}px)"
    >
      <div class="app-card p-0 overflow-hidden">
        {#if exceptionLine}
          <div class="flex items-center gap-3 p-4">
            <div class="size-9 rounded-full flex items-center justify-center shrink-0" style="background: var(--error-soft);">
              <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" style="color: var(--error);"><circle cx="12" cy="12" r="10"/><line x1="15" x2="9" y1="9" y2="15"/><line x1="9" x2="15" y1="9" y2="15"/></svg>
            </div>
            <div class="min-w-0">
              <p class="text-[10px] uppercase tracking-widest font-semibold" style="color: var(--error);">Exception</p>
              <p class="text-xs font-mono break-all">{exceptionLine.trim()}</p>
            </div>
          </div>
        {/if}
        {#if exceptionLine && threadLine}<hr class="app-divider" />{/if}
        {#if threadLine}
          <div class="flex items-center gap-3 p-4">
            <div class="size-9 rounded-full flex items-center justify-center shrink-0" style="background: var(--warning-soft);">
              <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" style="color: var(--warning);"><path d="m21.73 18-8-14a2 2 0 0 0-3.48 0l-8 14A2 2 0 0 0 4 21h16a2 2 0 0 0 1.73-3"/><path d="M12 9v4"/><path d="M12 17h.01"/></svg>
            </div>
            <div class="min-w-0">
              <p class="text-[10px] uppercase tracking-widest font-semibold" style="color: var(--warning);">Thread</p>
              <p class="text-xs font-mono break-all">{threadLine.replace("Thread:", "").replace("thread", "").trim()}</p>
            </div>
          </div>
        {/if}
      </div>
    </div>
  {/if}

  <!-- Stack Trace -->
  <div
    class="transition-all duration-400 ease-out"
    style:opacity={detailsVisible ? 1 : 0}
    style:transform="translateY({detailsVisible ? 0 : 15}px)"
  >
    <div class="app-card p-4 space-y-3">
      <div class="flex items-center justify-between">
        <div class="flex items-center gap-2">
          <svg xmlns="http://www.w3.org/2000/svg" width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" style="color: var(--error);"><circle cx="12" cy="12" r="10"/><line x1="15" x2="9" y1="9" y2="15"/><line x1="9" x2="15" y1="9" y2="15"/></svg>
          <span class="text-xs font-semibold uppercase tracking-widest" style="color: var(--error);">Stack Trace</span>
        </div>
        <span class="app-badge app-badge-error text-[10px]">{crashLines.length} lines</span>
      </div>
      <hr class="app-divider" />
      <div class="max-h-64 overflow-y-auto overflow-x-auto rounded-lg p-3" style="background: var(--input-bg);">
        <pre class="text-[11px] font-mono leading-relaxed select-text whitespace-pre" style="color: var(--text-secondary);">{crashLog}</pre>
      </div>
    </div>
  </div>

  <!-- Actions -->
  <div
    class="flex gap-3 pt-2 transition-all duration-400 ease-out"
    style:opacity={actionsVisible ? 1 : 0}
    style:transform="translateY({actionsVisible ? 0 : 10}px)"
  >
    <button type="button" class="app-btn app-btn-ghost flex-1 h-12 gap-2" onclick={copyLog}>
      <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><rect width="14" height="14" x="8" y="8" rx="2" ry="2"/><path d="M4 16c-1.1 0-2-.9-2-2V4c0-1.1.9-2 2-2h10c1.1 0 2 .9 2 2"/></svg>
      Copy Log
    </button>
    <button type="button" class="app-btn app-btn-error flex-1 h-12 gap-2" onclick={onrestart}>
      <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M21 12a9 9 0 0 0-9-9 9.75 9.75 0 0 0-6.74 2.74L3 8"/><path d="M3 3v5h5"/></svg>
      Restart
    </button>
  </div>
</div>
