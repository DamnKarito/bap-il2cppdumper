<script lang="ts">
  import lottie from "lottie-web";
  import { onMount } from "svelte";
  import { errorMessage, logs, resetAll, resetForNewDump, t } from "$lib/stores";

  let lottieContainer: HTMLDivElement;
  let headerVisible = $state(false);
  let logsVisible = $state(false);
  let actionsVisible = $state(false);

  onMount(() => {
    lottie.loadAnimation({
      container: lottieContainer,
      path: "/error.json",
      loop: false,
      autoplay: true,
      rendererSettings: { preserveAspectRatio: "xMidYMid slice" },
    });

    setTimeout(() => headerVisible = true, 100);
    setTimeout(() => logsVisible = true, 250);
    setTimeout(() => actionsVisible = true, 400);
  });
</script>

<div class="flex flex-col h-full p-4 gap-4 overflow-y-auto animate-slide-up">
  <!-- Error Header -->
  <div
    class="transition-all duration-400 ease-out"
    style:opacity={headerVisible ? 1 : 0}
    style:transform="translateY({headerVisible ? 0 : 20}px)"
  >
    <div class="app-card p-8 text-center space-y-4">
      <div class="size-28 mx-auto rounded-2xl flex items-center justify-center" style="background: var(--error-soft); box-shadow: inset 0 0 0 1px var(--error-ring);">
        <div bind:this={lottieContainer} class="size-20"></div>
      </div>
      <h2 class="text-xl font-bold" style="color: var(--error);">{$t.dump_failed}</h2>
      <div class="max-w-sm mx-auto px-4 py-2.5 rounded-xl" style="background: var(--error-soft); border: 1px solid var(--error-ring);">
        <p class="text-sm font-mono select-text break-all leading-relaxed" style="color: var(--error);">{$errorMessage}</p>
      </div>
    </div>
  </div>

  <!-- Error Logs -->
  {#if $logs.length > 0}
    <div
      class="transition-all duration-400 ease-out"
      style:opacity={logsVisible ? 1 : 0}
      style:transform="translateY({logsVisible ? 0 : 15}px)"
    >
      <div class="app-card p-4 space-y-3">
        <div class="flex items-center justify-between">
          <div class="flex items-center gap-2">
            <svg xmlns="http://www.w3.org/2000/svg" width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" style="color: var(--text-secondary);"><polyline points="4 17 10 11 4 5"/><line x1="12" x2="20" y1="19" y2="19"/></svg>
            <span class="text-xs font-semibold uppercase tracking-widest" style="color: var(--text-secondary);">{$t.label_log}</span>
          </div>
          <span class="app-badge app-badge-error text-[10px]">{$logs.length} entries</span>
        </div>
        <hr class="app-divider" />
        <div class="max-h-64 overflow-y-auto rounded-lg p-3 space-y-1" style="background: var(--input-bg);">
          {#each $logs as log}
            <div class="flex items-start gap-2 py-0.5">
              <span class="size-1.5 rounded-full mt-1.5 shrink-0" style="background: {log.startsWith('ERROR') ? 'var(--error)' : 'var(--text-secondary)'}; opacity: {log.startsWith('ERROR') ? '1' : '0.3'};"></span>
              <p class="text-[11px] font-mono break-all select-text leading-relaxed" style="color: {log.startsWith('ERROR') ? 'var(--error)' : 'var(--text-secondary)'};">{log}</p>
            </div>
          {/each}
        </div>
      </div>
    </div>
  {/if}

  <!-- Actions -->
  <div
    class="pt-2 space-y-3 transition-all duration-400 ease-out"
    style:opacity={actionsVisible ? 1 : 0}
    style:transform="translateY({actionsVisible ? 0 : 10}px)"
  >
    <button type="button" class="app-btn app-btn-error w-full h-14 text-sm font-semibold gap-2" onclick={resetForNewDump}>
      <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M21 12a9 9 0 0 0-9-9 9.75 9.75 0 0 0-6.74 2.74L3 8"/><path d="M3 3v5h5"/></svg>
      {$t.dump_again}
    </button>
    <button type="button" class="app-btn app-btn-ghost w-full h-12 text-sm font-semibold gap-2" onclick={resetAll}>
      <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M21 12a9 9 0 0 0-9-9 9.75 9.75 0 0 0-6.74 2.74L3 8"/><path d="M3 3v5h5"/><path d="M3 12a9 9 0 0 0 9 9 9.75 9.75 0 0 0 6.74-2.74L21 16"/><path d="M16 16h5v5"/></svg>
      {$t.try_again}
    </button>
  </div>
</div>
