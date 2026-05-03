<script lang="ts">
  import lottie from "lottie-web";
  import { onMount } from "svelte";
  import { logs, outputPath, resetAll, t } from "$lib/stores";

  let lottieContainer: HTMLDivElement;
  let headerVisible = $state(false);
  let pathVisible = $state(false);
  let logsVisible = $state(false);
  let actionsVisible = $state(false);

  function getLogStyle(log: string): { color: string; icon: boolean } {
    if (log.startsWith("Done!")) return { color: "var(--accent)", icon: true };
    if (log.includes("generated")) return { color: "var(--success)", icon: true };
    return { color: "var(--text-secondary)", icon: false };
  }

  onMount(() => {
    lottie.loadAnimation({
      container: lottieContainer,
      path: "/success.json",
      loop: false,
      autoplay: true,
      rendererSettings: { preserveAspectRatio: "xMidYMid slice" },
    });

    setTimeout(() => headerVisible = true, 100);
    setTimeout(() => pathVisible = true, 220);
    setTimeout(() => logsVisible = true, 340);
    setTimeout(() => actionsVisible = true, 460);
  });
</script>

<div class="flex flex-col h-full p-4 gap-4 overflow-y-auto animate-slide-up">
  <!-- Success Header -->
  <div
    class="transition-all duration-400 ease-out"
    style:opacity={headerVisible ? 1 : 0}
    style:transform="translateY({headerVisible ? 0 : 20}px)"
  >
    <div class="app-card p-8 text-center space-y-4">
      <div class="size-28 mx-auto rounded-2xl flex items-center justify-center" style="background: var(--accent-soft); box-shadow: inset 0 0 0 1px var(--accent-ring);">
        <div bind:this={lottieContainer} class="size-20"></div>
      </div>
      <h2 class="text-xl font-bold" style="color: var(--accent);">{$t.dump_complete}</h2>
    </div>
  </div>

  <!-- Output Path -->
  <div
    class="transition-all duration-400 ease-out"
    style:opacity={pathVisible ? 1 : 0}
    style:transform="translateY({pathVisible ? 0 : 15}px)"
  >
    <div class="app-card p-4">
      <div class="flex items-center gap-3">
        <div class="size-10 rounded-full flex items-center justify-center shrink-0" style="background: var(--accent-soft); box-shadow: inset 0 0 0 1px var(--accent-ring);">
          <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" style="color: var(--accent);"><path d="m6 14 1.45-2.9A2 2 0 0 1 9.24 10H20a2 2 0 0 1 1.94 2.5l-1.55 6a2 2 0 0 1-1.94 1.5H4a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h3.9a2 2 0 0 1 1.69.9l.81 1.2a2 2 0 0 0 1.67.9H18a2 2 0 0 1 2 2v2"/></svg>
        </div>
        <div class="min-w-0 flex-1">
          <p class="app-section-label">{$t.label_output_dir}</p>
          <p class="text-sm font-mono truncate select-text">{$outputPath}</p>
        </div>
      </div>
    </div>
  </div>

  <!-- Log Summary -->
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
          <span class="app-badge app-badge-muted text-[10px]">{$logs.length} entries</span>
        </div>
        <hr class="app-divider" />
        <div class="max-h-64 overflow-y-auto rounded-lg p-3 space-y-1" style="background: var(--input-bg);">
          {#each $logs as log}
            {@const style = getLogStyle(log)}
            <div class="flex items-start gap-2 py-0.5">
              {#if style.icon}
                <svg xmlns="http://www.w3.org/2000/svg" width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" class="mt-0.5 shrink-0" style="color: {style.color};"><path d="M22 11.08V12a10 10 0 1 1-5.93-9.14"/><polyline points="22 4 12 14.01 9 11.01"/></svg>
              {:else}
                <span class="size-1.5 rounded-full mt-1.5 shrink-0" style="background: var(--text-secondary); opacity: 0.3;"></span>
              {/if}
              <p class="text-[11px] font-mono break-all select-text leading-relaxed" style="color: {style.color};">{log}</p>
            </div>
          {/each}
        </div>
      </div>
    </div>
  {/if}

  <!-- Actions -->
  <div
    class="pt-2 transition-all duration-400 ease-out"
    style:opacity={actionsVisible ? 1 : 0}
    style:transform="translateY({actionsVisible ? 0 : 10}px)"
  >
    <button type="button" class="app-btn app-btn-primary w-full h-14 text-sm font-semibold gap-2" onclick={resetAll}>
      <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M21 12a9 9 0 0 0-9-9 9.75 9.75 0 0 0-6.74 2.74L3 8"/><path d="M3 3v5h5"/><path d="M3 12a9 9 0 0 0 9 9 9.75 9.75 0 0 0 6.74-2.74L21 16"/><path d="M16 16h5v5"/></svg>
      {$t.new_dump}
    </button>
  </div>
</div>
