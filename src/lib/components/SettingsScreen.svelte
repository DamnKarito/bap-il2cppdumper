<script lang="ts">
  import { open } from "@tauri-apps/plugin-dialog";
  import { themeMode, language, outputDir, defaultOutputDir, currentScreen, t, applyTheme } from "$lib/stores";
  import { LANGUAGES, type ThemeMode } from "$lib/i18n";
  import { Button } from "$lib/components/ui/button/index.js";
  import { Card, CardContent } from "$lib/components/ui/card/index.js";
  import { Input } from "$lib/components/ui/input/index.js";
  import { Separator } from "$lib/components/ui/separator/index.js";

  let langExpanded = $state(false);

  const THEME_OPTIONS: { mode: ThemeMode; icon: string }[] = [
    { mode: "system", icon: "◐" },
    { mode: "light", icon: "☀" },
    { mode: "dark", icon: "🌙" },
  ];

  function setTheme(mode: ThemeMode) {
    themeMode.set(mode);
    applyTheme(mode);
  }

  async function pickOutputDir() {
    const dir = await open({ directory: true, title: "Select Output Directory" });
    if (dir) outputDir.set(dir);
  }
</script>

<div class="flex flex-col h-full overflow-y-auto animate-slide-up">
  <div class="flex items-center gap-3 px-5 py-4 border-b border-border">
    <Button variant="ghost" size="icon" onclick={() => currentScreen.set("idle")}>
      <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="m12 19-7-7 7-7"/><path d="M19 12H5"/></svg>
    </Button>
    <h2 class="text-lg font-semibold">{$t.settings}</h2>
  </div>

  <div class="flex-1 overflow-y-auto p-4 space-y-3">
    <Card>
      <CardContent class="py-4 space-y-4">
        <div class="flex items-center gap-2">
          <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" class="text-primary"><circle cx="12" cy="12" r="4"/><path d="M12 2v2"/><path d="M12 20v2"/><path d="m4.93 4.93 1.41 1.41"/><path d="m17.66 17.66 1.41 1.41"/><path d="M2 12h2"/><path d="M20 12h2"/><path d="m6.34 17.66-1.41 1.41"/><path d="m19.07 4.93-1.41 1.41"/></svg>
          <span class="text-xs font-bold uppercase tracking-[0.15em] text-primary">{$t.label_appearance}</span>
        </div>
        <p class="text-sm text-muted-foreground">{$t.label_theme}</p>
        <div class="flex rounded-lg overflow-hidden border border-border">
          {#each THEME_OPTIONS as opt}
            <button
              class="flex-1 py-2.5 text-sm font-medium flex items-center justify-center gap-1.5 transition-all cursor-pointer
                {$themeMode === opt.mode ? 'bg-primary text-primary-foreground' : 'bg-card text-muted-foreground hover:text-foreground hover:bg-muted/50'}"
              onclick={() => setTheme(opt.mode)}
            >
              <span>{opt.icon}</span>
              {#if opt.mode === "system"}{$t.theme_system}{:else if opt.mode === "light"}{$t.theme_light}{:else}{$t.theme_dark}{/if}
            </button>
          {/each}
        </div>
      </CardContent>
    </Card>

    <Card>
      <button
        class="w-full text-left cursor-pointer"
        onclick={() => langExpanded = !langExpanded}
      >
        <CardContent class="py-4">
          <div class="flex items-center justify-between">
            <div class="flex items-center gap-3">
              <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" class="text-primary"><circle cx="12" cy="12" r="10"/><path d="M12 2a14.5 14.5 0 0 0 0 20 14.5 14.5 0 0 0 0-20"/><path d="M2 12h20"/></svg>
              <div>
                <p class="text-xs font-bold uppercase tracking-[0.15em] text-primary">{$t.label_language}</p>
                <p class="text-sm text-muted-foreground">{LANGUAGES.find(l => l.code === $language)?.displayName}</p>
              </div>
            </div>
            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" class="text-muted-foreground transition-transform {langExpanded ? 'rotate-180' : ''}"><path d="m6 9 6 6 6-6"/></svg>
          </div>
        </CardContent>
      </button>
      {#if langExpanded}
        <div class="border-t border-border">
          {#each LANGUAGES as lang}
            {@const isActive = $language === lang.code}
            <button
              class="w-full flex items-center justify-between px-6 py-3 text-sm transition-colors cursor-pointer
                {isActive ? 'text-primary bg-primary/5' : 'text-foreground hover:bg-muted/50'}"
              onclick={() => { language.set(lang.code); langExpanded = false; }}
            >
              <span class="font-medium">{lang.displayName}</span>
              {#if isActive}
                <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" class="text-primary"><polyline points="20 6 9 17 4 12"/></svg>
              {/if}
            </button>
          {/each}
        </div>
      {/if}
    </Card>

    <Card>
      <CardContent class="py-4 space-y-3">
        <div class="flex items-center gap-2">
          <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" class="text-primary"><path d="m6 14 1.45-2.9A2 2 0 0 1 9.24 10H20a2 2 0 0 1 1.94 2.5l-1.55 6a2 2 0 0 1-1.94 1.5H4a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h3.9a2 2 0 0 1 1.69.9l.81 1.2a2 2 0 0 0 1.67.9H18a2 2 0 0 1 2 2v2"/></svg>
          <span class="text-xs font-bold uppercase tracking-[0.15em] text-primary">{$t.label_output_dir}</span>
        </div>
        <p class="text-xs text-muted-foreground">{$t.setting_output_dir_desc}</p>
        <div class="flex items-center gap-2">
          <Input value={$outputDir} onchange={(e) => outputDir.set(e.currentTarget.value)} class="flex-1 font-mono text-xs" />
          <Button variant="outline" size="icon" onclick={pickOutputDir}>
            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="m6 14 1.45-2.9A2 2 0 0 1 9.24 10H20a2 2 0 0 1 1.94 2.5l-1.55 6a2 2 0 0 1-1.94 1.5H4a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h3.9a2 2 0 0 1 1.69.9l.81 1.2a2 2 0 0 0 1.67.9H18a2 2 0 0 1 2 2v2"/></svg>
          </Button>
        </div>
        {#if $outputDir !== $defaultOutputDir}
          <Button variant="outline" class="w-full" onclick={() => outputDir.set($defaultOutputDir)}>
            <svg xmlns="http://www.w3.org/2000/svg" width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M21 12a9 9 0 0 0-9-9 9.75 9.75 0 0 0-6.74 2.74L3 8"/><path d="M3 3v5h5"/></svg>
            {$t.output_reset}
          </Button>
        {/if}
      </CardContent>
    </Card>

    <Button variant="secondary" class="w-full justify-start gap-3 h-12" onclick={() => currentScreen.set("about")}>
      <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="12" cy="12" r="10"/><path d="M12 16v-4"/><path d="M12 8h.01"/></svg>
      {$t.label_about}
    </Button>
  </div>
</div>
