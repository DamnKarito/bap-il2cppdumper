<script lang="ts">
  import lottie from "lottie-web";
  import { onMount } from "svelte";
  import { currentScreen, t } from "$lib/stores";
  import { openUrl as tauriOpenUrl } from "@tauri-apps/plugin-opener";
  import { Button } from "$lib/components/ui/button/index.js";
  import { Card, CardContent } from "$lib/components/ui/card/index.js";
  import { Badge } from "$lib/components/ui/badge/index.js";
  import { Separator } from "$lib/components/ui/separator/index.js";

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

<div class="flex flex-col h-full overflow-y-auto animate-slide-up">
  <div class="flex items-center gap-3 px-5 py-4 border-b border-border">
    <Button variant="ghost" size="icon" onclick={() => currentScreen.set("settings")}>
      <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="m12 19-7-7 7-7"/><path d="M19 12H5"/></svg>
    </Button>
    <h2 class="text-lg font-semibold">{$t.label_about}</h2>
  </div>

  <div class="flex-1 overflow-y-auto p-4 space-y-4">
    <!-- Hero Section -->
    <div
      class="transition-all duration-400 ease-out"
      style:opacity={heroVisible ? 1 : 0}
      style:transform="translateY({heroVisible ? 0 : 20}px)"
    >
      <Card class="shadow-sm">
        <CardContent class="py-8 text-center space-y-4">
          <div class="size-32 mx-auto rounded-[32px] bg-primary/10 flex items-center justify-center ring-1 ring-primary/20 shadow-sm overflow-hidden">
            <div bind:this={logoContainer} class="size-[100px]"></div>
          </div>
          <div>
            <h3 class="text-2xl font-bold">{$t.app_name}</h3>
            <Badge variant="secondary" class="mt-2 bg-secondary/50 text-secondary-foreground px-4 py-1 text-xs">{$t.about_version}</Badge>
          </div>
          <p class="text-sm text-muted-foreground px-2">{$t.about_description}</p>
        </CardContent>
      </Card>
    </div>

    <!-- Developer Section -->
    <div
      class="transition-all duration-400 ease-out"
      style:opacity={devVisible ? 1 : 0}
      style:transform="translateY({devVisible ? 0 : 15}px)"
    >
      <Card>
        <CardContent class="py-4">
          <div class="flex items-center gap-4">
            <div class="size-6 text-primary flex items-center justify-center shrink-0">
              <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><polyline points="16 18 22 12 16 6"/><polyline points="8 6 2 12 8 18"/></svg>
            </div>
            <div>
              <p class="text-xs text-primary font-semibold uppercase tracking-widest leading-tight">{$t.about_developer}</p>
              <p class="text-[15px] font-semibold mt-0.5">Rodroid Mods</p>
              <p class="text-sm text-muted-foreground">{$t.about_powered_by}</p>
            </div>
          </div>
        </CardContent>
      </Card>
    </div>

    <!-- Community Section -->
    <div
      class="transition-all duration-400 ease-out"
      style:opacity={linksVisible ? 1 : 0}
      style:transform="translateY({linksVisible ? 0 : 15}px)"
    >
      <Card class="bg-muted/30 border-none">
        <CardContent class="py-0">
          <div class="flex items-center gap-3 px-0 py-4">
            <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" class="text-primary"><path d="m22 2-7 20-4-9-9-4Z"/><path d="M22 2 11 13"/></svg>
            <span class="text-xs font-bold uppercase tracking-[0.15em] text-primary">{$t.about_community}</span>
          </div>
          <Separator class="bg-border/50" />
          
          <button class="w-full flex items-center gap-4 py-3.5 cursor-pointer hover:bg-muted/50 -mx-6 px-6 transition-colors rounded-lg" onclick={() => openUrl(LINKS.channel1)}>
            <div class="size-10 rounded-full bg-primary/10 flex items-center justify-center shrink-0">
              <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" class="text-primary"><path d="m22 8-6 12-2.5-5.5L8 12l14-4Z"/></svg>
            </div>
            <div class="flex-1 text-left min-w-0">
              <p class="text-[15px] font-medium leading-tight">{$t.about_channel_1}</p>
              <p class="text-sm text-muted-foreground">{$t.about_channel_1_desc}</p>
            </div>
            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" class="text-muted-foreground shrink-0"><path d="M5 12h14"/><path d="m12 5 7 7-7 7"/></svg>
          </button>
          
          <button class="w-full flex items-center gap-4 py-3.5 cursor-pointer hover:bg-muted/50 -mx-6 px-6 transition-colors rounded-lg" onclick={() => openUrl(LINKS.channel2)}>
            <div class="size-10 rounded-full bg-primary/10 flex items-center justify-center shrink-0">
              <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" class="text-primary"><path d="m22 8-6 12-2.5-5.5L8 12l14-4Z"/></svg>
            </div>
            <div class="flex-1 text-left min-w-0">
              <p class="text-[15px] font-medium leading-tight">{$t.about_channel_2}</p>
              <p class="text-sm text-muted-foreground">{$t.about_channel_2_desc}</p>
            </div>
            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" class="text-muted-foreground shrink-0"><path d="M5 12h14"/><path d="m12 5 7 7-7 7"/></svg>
          </button>
          
          <button class="w-full flex items-center gap-4 py-3.5 cursor-pointer hover:bg-muted/50 -mx-6 px-6 transition-colors rounded-lg" onclick={() => openUrl(LINKS.group)}>
            <div class="size-10 rounded-full bg-primary/10 flex items-center justify-center shrink-0">
              <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" class="text-primary"><path d="M16 21v-2a4 4 0 0 0-4-4H6a4 4 0 0 0-4 4v2"/><circle cx="9" cy="7" r="4"/><path d="M22 21v-2a4 4 0 0 0-3-3.87"/><path d="M16 3.13a4 4 0 0 1 0 7.75"/></svg>
            </div>
            <div class="flex-1 text-left min-w-0">
              <p class="text-[15px] font-medium leading-tight">{$t.about_group}</p>
              <p class="text-sm text-muted-foreground">{$t.about_group_desc}</p>
            </div>
            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" class="text-muted-foreground shrink-0"><path d="M5 12h14"/><path d="m12 5 7 7-7 7"/></svg>
          </button>
          
          <Separator class="bg-border/50" />
          
          <button class="w-full flex items-center gap-4 py-3.5 cursor-pointer hover:bg-muted/50 -mx-6 px-6 transition-colors rounded-b-lg" onclick={() => openUrl(LINKS.bugs)}>
            <div class="size-10 rounded-full bg-primary/10 flex items-center justify-center shrink-0">
              <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" class="text-primary"><path d="m8 2 1.88 1.88"/><path d="M14.12 3.88 16 2"/><path d="M9 7.13v-1a3.003 3.003 0 1 1 6 0v1"/><path d="M12 20c-3.3 0-6-2.7-6-6v-3a4 4 0 0 1 4-4h4a4 4 0 0 1 4 4v3c0 3.3-2.7 6-6 6"/><path d="M12 20v-9"/><path d="M6.53 9C4.6 8.8 3 7.1 3 5"/><path d="M6 13H2"/><path d="M3 21c0-2.1 1.7-3.9 3.8-4"/><path d="M20.97 5c0 2.1-1.6 3.8-3.5 4"/><path d="M22 13h-4"/><path d="M17.2 17c2.1.1 3.8 1.9 3.8 4"/></svg>
            </div>
            <div class="flex-1 text-left min-w-0">
              <p class="text-[15px] font-medium leading-tight">{$t.about_report_bugs}</p>
              <p class="text-sm text-muted-foreground">@rodroidmods</p>
            </div>
            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" class="text-muted-foreground shrink-0"><path d="M5 12h14"/><path d="m12 5 7 7-7 7"/></svg>
          </button>
        </CardContent>
      </Card>
      
      <!-- Footer Like Animation -->
      <div 
        class="flex items-center justify-center gap-1 mt-4 transition-all duration-700 ease-out delay-500"
        style:opacity={linksVisible ? 1 : 0}
      >
        <div bind:this={likeContainer} class="size-8"></div>
        <p class="text-xs text-muted-foreground">Made with Rust by Rodroid Mods</p>
      </div>
    </div>
  </div>
</div>
