<script lang="ts">
  import lottie from "lottie-web";
  import { onMount } from "svelte";
  import { errorMessage, logs, resetAll, resetForNewDump, t } from "$lib/stores";
  import { Button } from "$lib/components/ui/button/index.js";
  import { Card, CardContent } from "$lib/components/ui/card/index.js";
  import { Badge } from "$lib/components/ui/badge/index.js";
  import { Separator } from "$lib/components/ui/separator/index.js";

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
  <div
    class="transition-all duration-400 ease-out"
    style:opacity={headerVisible ? 1 : 0}
    style:transform="translateY({headerVisible ? 0 : 20}px)"
  >
    <Card>
      <CardContent class="py-8 text-center space-y-4">
        <div class="size-28 mx-auto rounded-2xl bg-destructive/10 flex items-center justify-center ring-1 ring-destructive/20">
          <div bind:this={lottieContainer} class="size-20"></div>
        </div>
        <h2 class="text-xl font-bold text-destructive">{$t.dump_failed}</h2>
        <div class="max-w-sm mx-auto px-4 py-2.5 rounded-xl bg-destructive/10 border border-destructive/20">
          <p class="text-sm text-destructive font-mono select-text break-all leading-relaxed">{$errorMessage}</p>
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
            <Badge variant="destructive" class="text-[10px]">{$logs.length} entries</Badge>
          </div>
          <Separator />
          <div class="max-h-64 overflow-y-auto rounded-lg bg-muted/30 p-3 space-y-1">
            {#each $logs as log}
              <div class="flex items-start gap-2 py-0.5">
                <span class="size-1.5 rounded-full mt-1.5 shrink-0 {log.startsWith('ERROR') ? 'bg-destructive' : 'bg-muted-foreground/30'}"></span>
                <p class="text-[11px] font-mono break-all select-text leading-relaxed {log.startsWith('ERROR') ? 'text-destructive' : 'text-muted-foreground'}">{log}</p>
              </div>
            {/each}
          </div>
        </CardContent>
      </Card>
    </div>
  {/if}

  <div
    class="pt-2 space-y-3 transition-all duration-400 ease-out"
    style:opacity={actionsVisible ? 1 : 0}
    style:transform="translateY({actionsVisible ? 0 : 10}px)"
  >
    <Button variant="destructive" size="lg" class="w-full h-14 text-sm font-semibold gap-2" onclick={resetForNewDump}>
      <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M21 12a9 9 0 0 0-9-9 9.75 9.75 0 0 0-6.74 2.74L3 8"/><path d="M3 3v5h5"/></svg>
      {$t.dump_again}
    </Button>
    <Button variant="secondary" size="lg" class="w-full h-12 text-sm font-semibold gap-2" onclick={resetAll}>
      <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M21 12a9 9 0 0 0-9-9 9.75 9.75 0 0 0-6.74 2.74L3 8"/><path d="M3 3v5h5"/><path d="M3 12a9 9 0 0 0 9 9 9.75 9.75 0 0 0 6.74-2.74L21 16"/><path d="M16 16h5v5"/></svg>
      {$t.try_again}
    </Button>
  </div>
</div>
