<script lang="ts">
  import lottie from "lottie-web";
  import { onMount } from "svelte";
  import { t } from "$lib/stores";
  import { Button } from "$lib/components/ui/button/index.js";
  import { Card, CardContent } from "$lib/components/ui/card/index.js";
  import { Badge } from "$lib/components/ui/badge/index.js";
  import { Separator } from "$lib/components/ui/separator/index.js";

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
  <div
    class="transition-all duration-400 ease-out"
    style:opacity={headerVisible ? 1 : 0}
    style:transform="translateY({headerVisible ? 0 : 20}px)"
  >
    <Card>
      <CardContent class="py-8 text-center space-y-4">
        <div class="size-24 mx-auto rounded-2xl bg-destructive/10 flex items-center justify-center ring-1 ring-destructive/20">
          <div bind:this={lottieContainer} class="size-20"></div>
        </div>
        <h2 class="text-xl font-bold text-destructive">Application Crashed</h2>
        <p class="text-sm text-muted-foreground max-w-xs mx-auto">
          An unexpected error occurred. You can copy the crash report and restart.
        </p>
      </CardContent>
    </Card>
  </div>

  {#if exceptionLine || threadLine}
    <div
      class="transition-all duration-400 ease-out"
      style:opacity={detailsVisible ? 1 : 0}
      style:transform="translateY({detailsVisible ? 0 : 15}px)"
    >
      <Card>
        <CardContent class="py-0">
          {#if exceptionLine}
            <div class="flex items-center gap-3 py-3">
              <div class="size-9 rounded-full bg-destructive/10 flex items-center justify-center shrink-0">
                <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" class="text-destructive"><circle cx="12" cy="12" r="10"/><line x1="15" x2="9" y1="9" y2="15"/><line x1="9" x2="15" y1="9" y2="15"/></svg>
              </div>
              <div class="min-w-0">
                <p class="text-[10px] uppercase tracking-widest text-destructive font-semibold">Exception</p>
                <p class="text-xs font-mono text-foreground break-all">{exceptionLine.trim()}</p>
              </div>
            </div>
          {/if}
          {#if exceptionLine && threadLine}<Separator />{/if}
          {#if threadLine}
            <div class="flex items-center gap-3 py-3">
              <div class="size-9 rounded-full bg-yellow-500/10 flex items-center justify-center shrink-0">
                <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" class="text-yellow-500"><path d="m21.73 18-8-14a2 2 0 0 0-3.48 0l-8 14A2 2 0 0 0 4 21h16a2 2 0 0 0 1.73-3"/><path d="M12 9v4"/><path d="M12 17h.01"/></svg>
              </div>
              <div class="min-w-0">
                <p class="text-[10px] uppercase tracking-widest text-yellow-500 font-semibold">Thread</p>
                <p class="text-xs font-mono text-foreground break-all">{threadLine.replace("Thread:", "").replace("thread", "").trim()}</p>
              </div>
            </div>
          {/if}
        </CardContent>
      </Card>
    </div>
  {/if}

  <div
    class="transition-all duration-400 ease-out"
    style:opacity={detailsVisible ? 1 : 0}
    style:transform="translateY({detailsVisible ? 0 : 15}px)"
  >
    <Card>
      <CardContent class="py-4 space-y-3">
        <div class="flex items-center justify-between">
          <div class="flex items-center gap-2">
            <svg xmlns="http://www.w3.org/2000/svg" width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" class="text-destructive"><circle cx="12" cy="12" r="10"/><line x1="15" x2="9" y1="9" y2="15"/><line x1="9" x2="15" y1="9" y2="15"/></svg>
            <span class="text-xs font-semibold text-destructive uppercase tracking-widest">Stack Trace</span>
          </div>
          <Badge variant="destructive" class="text-[10px]">{crashLines.length} lines</Badge>
        </div>
        <Separator />
        <div class="max-h-64 overflow-y-auto overflow-x-auto rounded-lg bg-muted/30 p-3">
          <pre class="text-[11px] font-mono text-muted-foreground leading-relaxed select-text whitespace-pre">{crashLog}</pre>
        </div>
      </CardContent>
    </Card>
  </div>

  <div
    class="flex gap-3 pt-2 transition-all duration-400 ease-out"
    style:opacity={actionsVisible ? 1 : 0}
    style:transform="translateY({actionsVisible ? 0 : 10}px)"
  >
    <Button variant="secondary" class="flex-1 h-12 gap-2" onclick={copyLog}>
      <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><rect width="14" height="14" x="8" y="8" rx="2" ry="2"/><path d="M4 16c-1.1 0-2-.9-2-2V4c0-1.1.9-2 2-2h10c1.1 0 2 .9 2 2"/></svg>
      Copy Log
    </Button>
    <Button variant="destructive" class="flex-1 h-12 gap-2" onclick={onrestart}>
      <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M21 12a9 9 0 0 0-9-9 9.75 9.75 0 0 0-6.74 2.74L3 8"/><path d="M3 3v5h5"/></svg>
      Restart
    </Button>
  </div>
</div>
