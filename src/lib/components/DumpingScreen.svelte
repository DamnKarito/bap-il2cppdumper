<script lang="ts">
  import { logs, elapsedSeconds, t } from "$lib/stores";
  import { onMount, onDestroy, tick } from "svelte";
  import { Card, CardContent, CardHeader, CardTitle } from "$lib/components/ui/card/index.js";
  import { Badge } from "$lib/components/ui/badge/index.js";
  import { ScrollArea } from "$lib/components/ui/scroll-area/index.js";

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
    if (log.startsWith("ERROR")) return { color: "text-destructive", icon: "✕" };
    if (log.startsWith("Done!")) return { color: "text-primary", icon: "★" };
    if (log.includes("generated")) return { color: "text-emerald-400", icon: "✓" };
    if (log.startsWith("Warning")) return { color: "text-yellow-400", icon: "!" };
    if (log.includes("Detected") || log.includes("Found") || log.includes("Registration")) return { color: "text-primary", icon: "◆" };
    return { color: "text-muted-foreground", icon: "›" };
  }

  $effect(() => {
    if ($logs.length > 0 && logContainer) {
      tick().then(() => { logContainer.scrollTop = logContainer.scrollHeight; });
    }
  });
</script>

<div class="flex flex-col h-full p-4 gap-3">
  <Card>
    <CardContent class="py-4">
      <div class="flex items-center justify-between">
        <div class="flex items-center gap-3">
          <div class="size-10 rounded-xl bg-primary/10 flex items-center justify-center ring-1 ring-primary/20">
            <div class="size-5 border-2 border-primary border-t-transparent rounded-full animate-spin"></div>
          </div>
          <div>
            <p class="text-sm font-semibold text-foreground">{$t.status_processing}</p>
            <p class="text-xs text-muted-foreground">{$logs.length} operations</p>
          </div>
        </div>
        <div class="px-3 py-1.5 rounded-lg bg-card border border-border font-mono">
          <span class="text-sm font-bold text-primary tabular-nums">{formatTime($elapsedSeconds)}</span>
        </div>
      </div>
      <div class="mt-3 h-1.5 rounded-full bg-muted overflow-hidden">
        <div class="h-full rounded-full bg-primary animate-pulse" style="width: 100%"></div>
      </div>
    </CardContent>
  </Card>

  <Card class="flex-1 flex flex-col min-h-0 overflow-hidden">
    <CardHeader class="py-3 border-b border-border">
      <div class="flex items-center justify-between">
        <div class="flex items-center gap-2">
          <span class="size-2 rounded-full bg-emerald-400 animate-pulse"></span>
          <CardTitle class="text-xs font-semibold uppercase tracking-widest text-muted-foreground">Live Output</CardTitle>
        </div>
        <Badge variant="secondary" class="text-[10px]">{$logs.length} lines</Badge>
      </div>
    </CardHeader>
    <div bind:this={logContainer} class="flex-1 overflow-y-auto p-3 space-y-px">
      {#each $logs as log, i}
        {@const style = getLogStyle(log)}
        <div class="flex items-start gap-2 py-0.5 px-2 -mx-2 rounded-md hover:bg-muted/30 transition-colors group">
          <span class="text-[10px] font-mono text-muted-foreground/40 mt-1 w-5 text-right shrink-0 tabular-nums">{i + 1}</span>
          <span class="text-[10px] mt-1 {style.color} w-3 shrink-0 opacity-60">{style.icon}</span>
          <span class="text-[13px] font-mono {style.color} break-all leading-relaxed">{log}</span>
        </div>
      {/each}
      {#if $logs.length === 0}
        <div class="flex items-center justify-center h-full">
          <p class="text-sm text-muted-foreground">Waiting for output...</p>
        </div>
      {/if}
    </div>
  </Card>
</div>
