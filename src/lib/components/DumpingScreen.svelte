<script lang="ts">
  import { logs, elapsedSeconds, t } from "$lib/stores";
  import { onMount, onDestroy, tick } from "svelte";

  let logContainer: HTMLDivElement;
  let timer: ReturnType<typeof setInterval>;

  onMount(() => {
    timer = setInterval(() => elapsedSeconds.update(n => n + 1), 1000);
  });

  onDestroy(() => { if (timer) clearInterval(timer); });

  function formatTime(s: number): string {
    const m = Math.floor(s / 60);
    const sec = s % 60;
    return `${String(m).padStart(2, "0")}:${String(sec).padStart(2, "0")}`;
  }

  function getLogStyle(log: string): { color: string; icon: string } {
    if (log.startsWith("ERROR")) return { color: "var(--error)", icon: "\u2715" };
    if (log.startsWith("Done!")) return { color: "var(--accent)", icon: "\u2605" };
    if (log.includes("generated")) return { color: "var(--success)", icon: "\u2713" };
    if (log.startsWith("Warning")) return { color: "var(--warning)", icon: "!" };
    if (log.includes("Detected") || log.includes("Found") || log.includes("Registration")) return { color: "var(--accent)", icon: "\u25C6" };
    return { color: "var(--text-secondary)", icon: "\u203A" };
  }

  $effect(() => {
    if ($logs.length > 0 && logContainer) {
      tick().then(() => { logContainer.scrollTop = logContainer.scrollHeight; });
    }
  });
</script>

<div class="flex flex-col h-full p-4 gap-3">
  <!-- Status Card -->
  <div class="app-card p-4">
    <div class="flex items-center justify-between">
      <div class="flex items-center gap-3">
        <div class="size-10 rounded-xl flex items-center justify-center" style="background: var(--accent-soft); box-shadow: inset 0 0 0 1px var(--accent-ring);">
          <div class="size-5 border-2 rounded-full animate-spin" style="border-color: var(--accent); border-top-color: transparent;"></div>
        </div>
        <div>
          <p class="text-sm font-semibold">{$t.status_processing}</p>
          <p class="text-xs" style="color: var(--text-secondary);">{$logs.length} operations</p>
        </div>
      </div>
      <div class="px-3 py-1.5 rounded-lg font-mono" style="background: var(--input-bg); border: 1px solid var(--input-border);">
        <span class="text-sm font-bold tabular-nums" style="color: var(--accent);">{formatTime($elapsedSeconds)}</span>
      </div>
    </div>
    <div class="mt-3 h-1.5 rounded-full overflow-hidden" style="background: var(--input-bg);">
      <div class="h-full rounded-full animate-pulse" style="background: var(--accent); width: 100%;"></div>
    </div>
  </div>

  <!-- Log Card -->
  <div class="app-card flex-1 flex flex-col min-h-0 overflow-hidden">
    <div class="py-3 px-4 border-b" style="border-color: var(--card-border);">
      <div class="flex items-center justify-between">
        <div class="flex items-center gap-2">
          <span class="size-2 rounded-full animate-pulse" style="background: var(--success);"></span>
          <span class="text-xs font-semibold uppercase tracking-widest" style="color: var(--text-secondary);">Live Output</span>
        </div>
        <span class="app-badge app-badge-muted text-[10px]">{$logs.length} lines</span>
      </div>
    </div>
    <div bind:this={logContainer} class="flex-1 overflow-y-auto p-3 space-y-px">
      {#each $logs as log, i}
        {@const style = getLogStyle(log)}
        <div class="flex items-start gap-2 py-0.5 px-2 -mx-2 rounded-md transition-colors group" style:--log-color={style.color}>
          <span class="text-[10px] font-mono mt-1 w-5 text-right shrink-0 tabular-nums" style="color: var(--text-secondary); opacity: 0.5;">{i + 1}</span>
          <span class="text-[10px] mt-1 w-3 shrink-0 opacity-60" style="color: var(--log-color);">{style.icon}</span>
          <span class="text-[13px] font-mono break-all leading-relaxed" style="color: var(--log-color);">{log}</span>
        </div>
      {/each}
      {#if $logs.length === 0}
        <div class="flex items-center justify-center h-full">
          <p class="text-sm" style="color: var(--text-secondary);">Waiting for output...</p>
        </div>
      {/if}
    </div>
  </div>
</div>
