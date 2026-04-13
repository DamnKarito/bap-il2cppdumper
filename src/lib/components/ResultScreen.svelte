<script lang="ts">
  import lottie from "lottie-web";
  import { onMount } from "svelte";
  import { logs, outputPath, resetAll, t } from "$lib/stores";
  import { Button } from "$lib/components/ui/button/index.js";
  import { Card, CardContent } from "$lib/components/ui/card/index.js";
  import { Badge } from "$lib/components/ui/badge/index.js";
  import { Separator } from "$lib/components/ui/separator/index.js";

  let lottieContainer: HTMLDivElement;
  let headerVisible = $state(false);
  let pathVisible = $state(false);
  let logsVisible = $state(false);
  let actionsVisible = $state(false);

  function getLogStyle(log: string): { color: string; icon: boolean } {
    if (log.startsWith("Done!")) return { color: "text-primary", icon: true };
    if (log.includes("generated")) return { color: "text-emerald-400", icon: true };
    return { color: "text-muted-foreground", icon: false };
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
  <div
    class="transition-all duration-400 ease-out"
    style:opacity={headerVisible ? 1 : 0}
    style:transform="translateY({headerVisible ? 0 : 20}px)"
  >
    <Card>
      <CardContent class="py-8 text-center space-y-4">
        <div class="size-28 mx-auto rounded-2xl bg-primary/10 flex items-center justify-center ring-1 ring-primary/20">
          <div bind:this={lottieContainer} class="size-20"></div>
        </div>
        <h2 class="text-xl font-bold text-primary">{$t.dump_complete}</h2>
      </CardContent>
    </Card>
  </div>

  <div
    class="transition-all duration-400 ease-out"
    style:opacity={pathVisible ? 1 : 0}
    style:transform="translateY({pathVisible ? 0 : 15}px)"
  >
    <Card>
      <CardContent class="py-3">
        <div class="flex items-center gap-3">
          <div class="size-10 rounded-full bg-primary/10 flex items-center justify-center shrink-0 ring-1 ring-primary/20">
            <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" class="text-primary"><path d="m6 14 1.45-2.9A2 2 0 0 1 9.24 10H20a2 2 0 0 1 1.94 2.5l-1.55 6a2 2 0 0 1-1.94 1.5H4a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h3.9a2 2 0 0 1 1.69.9l.81 1.2a2 2 0 0 0 1.67.9H18a2 2 0 0 1 2 2v2"/></svg>
          </div>
          <div class="min-w-0 flex-1">
            <p class="text-[10px] font-semibold uppercase tracking-widest text-primary">{$t.label_output_dir}</p>
            <p class="text-sm font-mono text-foreground truncate select-text">{$outputPath}</p>
          </div>
        </div>
      </CardContent>
    </Card>
  </div>

  {#if $logs.length > 0}
    <div
      class="transition-all duration-400 ease-out"
      style:opacity={logsVisible ? 1 : 0}
      style:transform="translateY({logsVisible ? 0 : 15}px)"
    >
      <Card>
        <CardContent class="py-4 space-y-3">
          <div class="flex items-center justify-between">
            <div class="flex items-center gap-2">
              <svg xmlns="http://www.w3.org/2000/svg" width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" class="text-muted-foreground"><polyline points="4 17 10 11 4 5"/><line x1="12" x2="20" y1="19" y2="19"/></svg>
              <span class="text-xs font-semibold uppercase tracking-widest text-muted-foreground">{$t.label_log}</span>
            </div>
            <Badge variant="secondary" class="text-[10px]">{$logs.length} entries</Badge>
          </div>
          <Separator />
          <div class="max-h-64 overflow-y-auto rounded-lg bg-muted/30 p-3 space-y-1">
            {#each $logs as log}
              {@const style = getLogStyle(log)}
              <div class="flex items-start gap-2 py-0.5">
                {#if style.icon}
                  <svg xmlns="http://www.w3.org/2000/svg" width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" class="{style.color} mt-0.5 shrink-0"><path d="M22 11.08V12a10 10 0 1 1-5.93-9.14"/><polyline points="22 4 12 14.01 9 11.01"/></svg>
                {:else}
                  <span class="size-1.5 rounded-full bg-muted-foreground/30 mt-1.5 shrink-0"></span>
                {/if}
                <p class="text-[11px] font-mono {style.color} break-all select-text leading-relaxed">{log}</p>
              </div>
            {/each}
          </div>
        </CardContent>
      </Card>
    </div>
  {/if}

  <div
    class="pt-2 transition-all duration-400 ease-out"
    style:opacity={actionsVisible ? 1 : 0}
    style:transform="translateY({actionsVisible ? 0 : 10}px)"
  >
    <Button size="lg" class="w-full h-14 text-sm font-semibold gap-2" onclick={resetAll}>
      <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M21 12a9 9 0 0 0-9-9 9.75 9.75 0 0 0-6.74 2.74L3 8"/><path d="M3 3v5h5"/><path d="M3 12a9 9 0 0 0 9 9 9.75 9.75 0 0 0 6.74-2.74L21 16"/><path d="M16 16h5v5"/></svg>
      {$t.new_dump}
    </Button>
  </div>
</div>
