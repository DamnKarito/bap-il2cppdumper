<script lang="ts">
  import lottie from "lottie-web";
  import { onMount } from "svelte";
  import { currentScreen, t } from "$lib/stores";
  import { openUrl as tauriOpenUrl } from "@tauri-apps/plugin-opener";

  const LINKS = {
    channel1: "https://t.me/+WmudnO0-xoNhMDQ8",
    channel2: "https://t.me/+WLLFw3pr-aVmMjBk",
    group: "https://t.me/+QylrYL1GNsJiYjc0",
    bugs: "https://t.me/rodroidmods",
  };

  async function openUrl(url: string) {
    try {
      await tauriOpenUrl(url);
    } catch (e) {
      console.error("Failed to open URL", e);
    }
  }

  let logoContainer: HTMLDivElement;
  let likeContainer: HTMLDivElement;

  let heroVisible = $state(false);
  let devVisible = $state(false);
  let linksVisible = $state(false);

  onMount(() => {
    lottie.loadAnimation({
      container: logoContainer,
      path: "/android_logo.json",
      loop: true,
      autoplay: true,
      rendererSettings: { preserveAspectRatio: "xMidYMid slice" },
    });

    lottie.loadAnimation({
      container: likeContainer,
      path: "/like.json",
      loop: false,
      autoplay: true,
      rendererSettings: { preserveAspectRatio: "xMidYMid slice" },
    });

    setTimeout(() => heroVisible = true, 100);
    setTimeout(() => devVisible = true, 220);
    setTimeout(() => linksVisible = true, 340);
  });
</script>

<div class="flex flex-col h-full overflow-y-auto animate-slide-up" style="background: var(--app-bg);">
  <!-- Header -->
  <div class="flex items-center gap-3 px-5 py-4 border-b" style="border-color: var(--card-border);">
    <button type="button" class="app-btn-icon" aria-label="Back" onclick={() => currentScreen.set("settings")}>
      <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="m12 19-7-7 7-7"/><path d="M19 12H5"/></svg>
    </button>
    <h2 class="text-lg font-semibold">{$t.label_about}</h2>
  </div>

  <div class="flex-1 overflow-y-auto p-4 space-y-4">
    <!-- Hero Section -->
    <div
      class="transition-all duration-400 ease-out"
      style:opacity={heroVisible ? 1 : 0}
      style:transform="translateY({heroVisible ? 0 : 20}px)"
    >
      <div class="app-card p-8 text-center space-y-4">
        <div class="size-32 mx-auto rounded-[32px] flex items-center justify-center overflow-hidden" style="background: var(--accent-soft); box-shadow: inset 0 0 0 1px var(--accent-ring);">
          <div bind:this={logoContainer} class="size-[100px]"></div>
        </div>
        <div>
          <h3 class="text-2xl font-bold">{$t.app_name}</h3>
          <span class="app-badge app-badge-muted mt-2 px-4 py-1 text-xs">{$t.about_version}</span>
        </div>
        <p class="text-sm px-2" style="color: var(--text-secondary);">{$t.about_description}</p>
      </div>
    </div>

    <!-- Developer Section -->
    <div
      class="transition-all duration-400 ease-out"
      style:opacity={devVisible ? 1 : 0}
      style:transform="translateY({devVisible ? 0 : 15}px)"
    >
      <div class="app-card p-4">
        <div class="flex items-center gap-4">
          <div class="size-6 flex items-center justify-center shrink-0" style="color: var(--accent);">
            <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><polyline points="16 18 22 12 16 6"/><polyline points="8 6 2 12 8 18"/></svg>
          </div>
          <div>
            <p class="app-section-label leading-tight">{$t.about_developer}</p>
            <p class="text-[15px] font-semibold mt-0.5">Rodroid Mods</p>
            <p class="text-sm" style="color: var(--text-secondary);">{$t.about_powered_by}</p>
          </div>
        </div>
      </div>
    </div>

    <!-- Community Section -->
    <div
      class="transition-all duration-400 ease-out"
      style:opacity={linksVisible ? 1 : 0}
      style:transform="translateY({linksVisible ? 0 : 15}px)"
    >
      <div class="app-card p-0 overflow-hidden">
        <div class="flex items-center gap-3 px-4 py-4">
          <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" style="color: var(--accent);"><path d="m22 2-7 20-4-9-9-4Z"/><path d="M22 2 11 13"/></svg>
          <span class="app-section-label">{$t.about_community}</span>
        </div>
        <hr class="app-divider" />

        {#each [
          { url: LINKS.channel1, title: $t.about_channel_1, desc: $t.about_channel_1_desc, icon: "channel" },
          { url: LINKS.channel2, title: $t.about_channel_2, desc: $t.about_channel_2_desc, icon: "channel" },
          { url: LINKS.group, title: $t.about_group, desc: $t.about_group_desc, icon: "group" },
        ] as link}
          <button class="w-full flex items-center gap-4 py-3.5 px-4 cursor-pointer transition-colors" style="background: transparent;" onmouseenter={(e) => e.currentTarget.style.background = 'var(--hover-bg)'} onmouseleave={(e) => e.currentTarget.style.background = 'transparent'} onclick={() => openUrl(link.url)}>
            <div class="size-10 rounded-full flex items-center justify-center shrink-0" style="background: var(--accent-soft);">
              {#if link.icon === "group"}
                <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" style="color: var(--accent);"><path d="M16 21v-2a4 4 0 0 0-4-4H6a4 4 0 0 0-4 4v2"/><circle cx="9" cy="7" r="4"/><path d="M22 21v-2a4 4 0 0 0-3-3.87"/><path d="M16 3.13a4 4 0 0 1 0 7.75"/></svg>
              {:else}
                <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" style="color: var(--accent);"><path d="m22 8-6 12-2.5-5.5L8 12l14-4Z"/></svg>
              {/if}
            </div>
            <div class="flex-1 text-left min-w-0">
              <p class="text-[15px] font-medium leading-tight">{link.title}</p>
              <p class="text-sm" style="color: var(--text-secondary);">{link.desc}</p>
            </div>
            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" style="color: var(--text-secondary);" class="shrink-0"><path d="M5 12h14"/><path d="m12 5 7 7-7 7"/></svg>
          </button>
        {/each}

        <hr class="app-divider" />

        <button class="w-full flex items-center gap-4 py-3.5 px-4 cursor-pointer transition-colors" style="background: transparent;" onmouseenter={(e) => e.currentTarget.style.background = 'var(--hover-bg)'} onmouseleave={(e) => e.currentTarget.style.background = 'transparent'} onclick={() => openUrl(LINKS.bugs)}>
          <div class="size-10 rounded-full flex items-center justify-center shrink-0" style="background: var(--accent-soft);">
            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" style="color: var(--accent);"><path d="m8 2 1.88 1.88"/><path d="M14.12 3.88 16 2"/><path d="M9 7.13v-1a3.003 3.003 0 1 1 6 0v1"/><path d="M12 20c-3.3 0-6-2.7-6-6v-3a4 4 0 0 1 4-4h4a4 4 0 0 1 4 4v3c0 3.3-2.7 6-6 6"/><path d="M12 20v-9"/><path d="M6.53 9C4.6 8.8 3 7.1 3 5"/><path d="M6 13H2"/><path d="M3 21c0-2.1 1.7-3.9 3.8-4"/><path d="M20.97 5c0 2.1-1.6 3.8-3.5 4"/><path d="M22 13h-4"/><path d="M17.2 17c2.1.1 3.8 1.9 3.8 4"/></svg>
          </div>
          <div class="flex-1 text-left min-w-0">
            <p class="text-[15px] font-medium leading-tight">{$t.about_report_bugs}</p>
            <p class="text-sm" style="color: var(--text-secondary);">@rodroidmods</p>
          </div>
          <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" style="color: var(--text-secondary);" class="shrink-0"><path d="M5 12h14"/><path d="m12 5 7 7-7 7"/></svg>
        </button>
      </div>

      <!-- Footer Like Animation -->
      <div
        class="flex items-center justify-center gap-1 mt-4 transition-all duration-700 ease-out delay-500"
        style:opacity={linksVisible ? 1 : 0}
      >
        <div bind:this={likeContainer} class="size-8"></div>
        <p class="text-xs" style="color: var(--text-secondary);">Made with Rust by Rodroid Mods</p>
      </div>
    </div>
  </div>
</div>
