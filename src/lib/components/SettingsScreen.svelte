<script lang="ts">
  import { open } from "@tauri-apps/plugin-dialog";
  import { themeMode, language, outputDir, defaultOutputDir, currentScreen, t, applyTheme } from "$lib/stores";
  import { LANGUAGES, type ThemeMode } from "$lib/i18n";

  let langExpanded = $state(false);

  const THEME_OPTIONS: { mode: ThemeMode; label: string; icon: string }[] = [
    { mode: "system", label: "system", icon: "\u25D0" },
    { mode: "light", label: "light", icon: "\u2600" },
    { mode: "dark", label: "dark", icon: "\u263E" },
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

<div class="flex flex-col h-full overflow-y-auto animate-slide-up" style="background: var(--app-bg);">
  <!-- Header -->
  <div class="flex items-center gap-3 px-5 py-4 border-b" style="border-color: var(--card-border);">
    <button type="button" class="app-btn-icon" aria-label="Back" onclick={() => currentScreen.set("idle")}>
      <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="m12 19-7-7 7-7"/><path d="M19 12H5"/></svg>
    </button>
    <h2 class="text-lg font-semibold">{$t.settings}</h2>
  </div>

  <div class="flex-1 overflow-y-auto p-4 space-y-3">
    <!-- Theme Card -->
    <div class="app-card p-4 space-y-4">
      <div class="flex items-center gap-2">
        <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" style="color: var(--accent);"><circle cx="12" cy="12" r="4"/><path d="M12 2v2"/><path d="M12 20v2"/><path d="m4.93 4.93 1.41 1.41"/><path d="m17.66 17.66 1.41 1.41"/><path d="M2 12h2"/><path d="M20 12h2"/><path d="m6.34 17.66-1.41 1.41"/><path d="m19.07 4.93-1.41 1.41"/></svg>
        <span class="app-section-label">{$t.label_appearance}</span>
      </div>
      <p class="text-sm" style="color: var(--text-secondary);">{$t.label_theme}</p>
      <div class="theme-toggle-group">
        {#each THEME_OPTIONS as opt}
          <button
            type="button"
            class="theme-toggle-btn {$themeMode === opt.mode ? 'active' : ''}"
            onclick={() => setTheme(opt.mode)}
          >
            <span>{opt.icon}</span>
            {#if opt.mode === "system"}{$t.theme_system}{:else if opt.mode === "light"}{$t.theme_light}{:else}{$t.theme_dark}{/if}
          </button>
        {/each}
      </div>
    </div>

    <!-- Language Card -->
    <div class="app-card overflow-hidden">
      <button
        class="w-full text-left cursor-pointer p-4"
        onclick={() => langExpanded = !langExpanded}
      >
        <div class="flex items-center justify-between">
          <div class="flex items-center gap-3">
            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" style="color: var(--accent);"><circle cx="12" cy="12" r="10"/><path d="M12 2a14.5 14.5 0 0 0 0 20 14.5 14.5 0 0 0 0-20"/><path d="M2 12h20"/></svg>
            <div>
              <p class="app-section-label">{$t.label_language}</p>
              <p class="text-sm" style="color: var(--text-secondary);">{LANGUAGES.find(l => l.code === $language)?.displayName}</p>
            </div>
          </div>
          <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" class="transition-transform {langExpanded ? 'rotate-180' : ''}" style="color: var(--text-secondary);"><path d="m6 9 6 6 6-6"/></svg>
        </div>
      </button>
      {#if langExpanded}
        <div class="border-t" style="border-color: var(--card-border);">
          {#each LANGUAGES as lang}
            {@const isActive = $language === lang.code}
            <button
              class="w-full flex items-center justify-between px-6 py-3 text-sm transition-colors cursor-pointer"
              style="background: {isActive ? 'var(--accent-soft)' : 'transparent'}; color: {isActive ? 'var(--accent)' : 'var(--text-primary)'};"
              onmouseenter={(e) => { if (!isActive) e.currentTarget.style.background = 'var(--hover-bg)'; }}
              onmouseleave={(e) => { if (!isActive) e.currentTarget.style.background = 'transparent'; }}
              onclick={() => { language.set(lang.code); langExpanded = false; }}
            >
              <span class="font-medium">{lang.displayName}</span>
              {#if isActive}
                <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" style="color: var(--accent);"><polyline points="20 6 9 17 4 12"/></svg>
              {/if}
            </button>
          {/each}
        </div>
      {/if}
    </div>

    <!-- Output Dir Card -->
    <div class="app-card p-4 space-y-3">
      <div class="flex items-center gap-2">
        <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" style="color: var(--accent);"><path d="m6 14 1.45-2.9A2 2 0 0 1 9.24 10H20a2 2 0 0 1 1.94 2.5l-1.55 6a2 2 0 0 1-1.94 1.5H4a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h3.9a2 2 0 0 1 1.69.9l.81 1.2a2 2 0 0 0 1.67.9H18a2 2 0 0 1 2 2v2"/></svg>
        <span class="app-section-label">{$t.label_output_dir}</span>
      </div>
      <p class="text-xs" style="color: var(--text-secondary);">{$t.setting_output_dir_desc}</p>
      <div class="flex items-center gap-2">
        <input type="text" value={$outputDir} onchange={(e) => outputDir.set(e.currentTarget.value)} class="app-input flex-1 font-mono text-xs" />
        <button type="button" class="app-btn-icon" aria-label="Browse output directory" onclick={pickOutputDir}>
          <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="m6 14 1.45-2.9A2 2 0 0 1 9.24 10H20a2 2 0 0 1 1.94 2.5l-1.55 6a2 2 0 0 1-1.94 1.5H4a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h3.9a2 2 0 0 1 1.69.9l.81 1.2a2 2 0 0 0 1.67.9H18a2 2 0 0 1 2 2v2"/></svg>
        </button>
      </div>
      {#if $outputDir !== $defaultOutputDir}
        <button type="button" class="app-btn app-btn-ghost w-full gap-2" onclick={() => outputDir.set($defaultOutputDir)}>
          <svg xmlns="http://www.w3.org/2000/svg" width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M21 12a9 9 0 0 0-9-9 9.75 9.75 0 0 0-6.74 2.74L3 8"/><path d="M3 3v5h5"/></svg>
          {$t.output_reset}
        </button>
      {/if}
    </div>

    <!-- About Button -->
    <button type="button" class="app-btn app-btn-ghost w-full justify-start gap-3 h-12" onclick={() => currentScreen.set("about")}>
      <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="12" cy="12" r="10"/><path d="M12 16v-4"/><path d="M12 8h.01"/></svg>
      {$t.label_about}
    </button>
  </div>
</div>
