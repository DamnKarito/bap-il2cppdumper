<script lang="ts">
  import type { DumperConfig } from "$lib/types";
  import { t } from "$lib/stores";

  let { config = $bindable(), onclose }: { config: DumperConfig; onclose: () => void } = $props();
</script>

<!-- Config Dialog Overlay -->
<div class="app-dialog">
  <button type="button" class="app-backdrop cursor-default" aria-label="Close dialog" onclick={onclose}></button>
  <div class="app-card w-full max-w-lg mx-4 shadow-2xl z-10 max-h-[85vh] flex flex-col overflow-hidden">
    <!-- Header -->
    <div class="p-6 pb-4 shrink-0">
      <div class="flex items-center gap-2.5">
        <div class="size-8 rounded-lg flex items-center justify-center" style="background: var(--accent-soft); box-shadow: inset 0 0 0 1px var(--accent-ring);">
          <svg xmlns="http://www.w3.org/2000/svg" width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" style="color: var(--accent);"><path d="M12.22 2h-.44a2 2 0 0 0-2 2v.18a2 2 0 0 1-1 1.73l-.43.25a2 2 0 0 1-2 0l-.15-.08a2 2 0 0 0-2.73.73l-.22.38a2 2 0 0 0 .73 2.73l.15.1a2 2 0 0 1 1 1.72v.51a2 2 0 0 1-1 1.74l-.15.09a2 2 0 0 0-.73 2.73l.22.38a2 2 0 0 0 2.73.73l.15-.08a2 2 0 0 1 2 0l.43.25a2 2 0 0 1 1 1.73V20a2 2 0 0 0 2 2h.44a2 2 0 0 0 2-2v-.18a2 2 0 0 1 1-1.73l.43-.25a2 2 0 0 1 2 0l.15.08a2 2 0 0 0 2.73-.73l.22-.39a2 2 0 0 0-.73-2.73l-.15-.08a2 2 0 0 1-1-1.74v-.5a2 2 0 0 1 1-1.74l.15-.09a2 2 0 0 0 .73-2.73l-.22-.38a2 2 0 0 0-2.73-.73l-.15.08a2 2 0 0 1-2 0l-.43-.25a2 2 0 0 1-1-1.73V4a2 2 0 0 0-2-2z"/><circle cx="12" cy="12" r="3"/></svg>
        </div>
        <div class="flex-1">
          <h3 class="text-lg font-semibold">{$t.dump_options}</h3>
          <p class="text-xs" style="color: var(--text-secondary);">Configure what gets included in the dump output.</p>
        </div>
        <button type="button" class="app-btn app-btn-ghost app-btn-icon shrink-0" onclick={onclose} aria-label="Close">
          <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><line x1="18" y1="6" x2="6" y2="18"/><line x1="6" y1="6" x2="18" y2="18"/></svg>
        </button>
      </div>
    </div>

    <!-- Scrollable Content -->
    <div class="flex-1 overflow-y-auto px-6 pb-2 space-y-5">
      <!-- Output Section -->
      <section>
        <p class="app-section-label mb-3">{$t.label_output}</p>
        <div class="space-y-3">
          {#each [
            { label: $t.setting_dump_method, key: "dumpMethod" },
            { label: $t.setting_dump_field, key: "dumpField" },
            { label: $t.setting_dump_property, key: "dumpProperty" },
            { label: $t.setting_dump_attribute, key: "dumpAttribute" },
            { label: $t.setting_dump_method_offset, key: "dumpMethodOffset" },
            { label: $t.setting_dump_field_offset, key: "dumpFieldOffset" },
            { label: $t.setting_dump_typedef_index, key: "dumpTypeDefIndex" },
            { label: $t.setting_dump_assembly_name, key: "dumpAssemblyName" },
            { label: $t.setting_split_dump_per_type, key: "splitDumpPerType" },
          ] as item}
            <div class="flex items-center justify-between">
              <span class="text-sm">{item.label}</span>
              <button type="button" class="app-toggle {config[item.key as keyof DumperConfig] ? 'active' : ''}" aria-label={item.label} onclick={() => (config[item.key as keyof DumperConfig] as any) = !config[item.key as keyof DumperConfig]}></button>
            </div>
          {/each}
        </div>
      </section>

      <hr class="app-divider" />

      <!-- Generation Section -->
      <section>
        <p class="app-section-label mb-3">{$t.label_generation}</p>
        <div class="space-y-3">
          {#each [
            { label: $t.setting_generate_struct, key: "generateStruct" },
            { label: $t.setting_generate_dummy_dll, key: "generateDummyDll" },
            { label: $t.setting_dummy_dll_add_token, key: "dummyDllAddToken" },
            { label: $t.setting_generate_generics_dump, key: "generateGenericsDump" },
          ] as item}
            <div class="flex items-center justify-between">
              <span class="text-sm">{item.label}</span>
              <button type="button" class="app-toggle {config[item.key as keyof DumperConfig] ? 'active' : ''}" aria-label={item.label} onclick={() => (config[item.key as keyof DumperConfig] as any) = !config[item.key as keyof DumperConfig]}></button>
            </div>
          {/each}
        </div>
      </section>

      <hr class="app-divider" />

      <!-- C++ Headers Section -->
      <section>
        <p class="app-section-label mb-3">{$t.label_cpp_headers}</p>
        <div class="space-y-3">
          {#each [
            { label: $t.setting_generate_cpp_scaffold, key: "generateCppScaffold" },
            { label: $t.setting_mangle_names, key: "mangleNames" },
            { label: $t.setting_enhanced_ida_metadata, key: "enhancedIdaMetadata" },
            { label: $t.setting_generate_unity_headers, key: "generateUnityHeaders" },
            { label: $t.setting_use_topological_sort, key: "useTopologicalSort" },
          ] as item}
            <div class="flex items-center justify-between">
              <span class="text-sm">{item.label}</span>
              <button type="button" class="app-toggle {config[item.key as keyof DumperConfig] ? 'active' : ''}" aria-label={item.label} onclick={() => (config[item.key as keyof DumperConfig] as any) = !config[item.key as keyof DumperConfig]}></button>
            </div>
          {/each}
          <div class="space-y-2">
            <span class="text-xs" style="color: var(--text-secondary);">{$t.setting_compiler_layout}</span>
            <div class="theme-toggle-group">
              {#each [$t.layout_gcc, $t.layout_msvc] as layout, i}
                <button
                  type="button"
                  class="theme-toggle-btn {config.compilerLayout === (i === 0 ? 'GCC' : 'MSVC') ? 'active' : ''}"
                  onclick={() => config.compilerLayout = i === 0 ? 'GCC' : 'MSVC'}
                >{layout}</button>
              {/each}
            </div>
          </div>
        </div>
      </section>

      <hr class="app-divider" />

      <!-- Disassembly Section -->
      <section>
        <p class="app-section-label mb-3">{$t.label_disassembly}</p>
        <div class="space-y-3">
          <div class="flex items-center justify-between">
            <span class="text-sm">{$t.setting_dump_disassembly}</span>
            <button type="button" class="app-toggle {config.dumpDisassembly ? 'active' : ''}" aria-label={$t.setting_dump_disassembly} onclick={() => config.dumpDisassembly = !config.dumpDisassembly}></button>
          </div>
          {#if config.dumpDisassembly}
            <div class="pl-4 space-y-3 border-l-2" style="border-color: var(--accent-ring);">
              <div class="space-y-2">
                <span class="text-xs" style="color: var(--text-secondary);">Target</span>
                <div class="theme-toggle-group">
                  {#each [$t.target_both, $t.target_dump_cs, $t.target_diffable_cs] as target, i}
                    <button
                      type="button"
                      class="theme-toggle-btn text-xs {config.dumpDisassemblyTarget === i ? 'active' : ''}"
                      onclick={() => config.dumpDisassemblyTarget = i}
                    >{target}</button>
                  {/each}
                </div>
              </div>
              {#each [
                { label: $t.setting_dump_disassembly_hex_bytes, key: "dumpDisassemblyHexBytes" },
                { label: $t.setting_dump_disassembly_field_names, key: "dumpDisassemblyFieldNames" },
                { label: $t.setting_dump_disassembly_annotations, key: "dumpDisassemblyAnnotations" },
                { label: $t.setting_dump_disassembly_cfg, key: "dumpDisassemblyCfg" },
              ] as item}
                <div class="flex items-center justify-between">
                  <span class="text-sm">{item.label}</span>
                  <button type="button" class="app-toggle {config[item.key as keyof DumperConfig] ? 'active' : ''}" aria-label={item.label} onclick={() => (config[item.key as keyof DumperConfig] as any) = !config[item.key as keyof DumperConfig]}></button>
                </div>
              {/each}
              <div class="flex items-center justify-between">
                <span class="text-sm">{$t.setting_max_disassembly_instructions}</span>
                <input
                  type="number"
                  bind:value={config.maxDisassemblyInstructions}
                  min={32} max={4096}
                  class="app-input w-24 text-right font-mono text-sm"
                />
              </div>
            </div>
          {/if}
        </div>
      </section>

      <hr class="app-divider" />

      <!-- Advanced Section -->
      <section>
        <p class="app-section-label mb-3">{$t.label_advanced}</p>
        <div class="space-y-3">
          <div class="flex items-center justify-between">
            <span class="text-sm">{$t.setting_force_il2cpp_version}</span>
            <button type="button" class="app-toggle {config.forceIl2cppVersion ? 'active' : ''}" aria-label={$t.setting_force_il2cpp_version} onclick={() => config.forceIl2cppVersion = !config.forceIl2cppVersion}></button>
          </div>
          {#if config.forceIl2cppVersion}
            <div class="flex items-center justify-between pl-4 border-l-2" style="border-color: var(--accent-ring);">
              <span class="text-sm" style="color: var(--text-secondary);">{$t.setting_force_version_label}</span>
              <input type="number" bind:value={config.forceVersion} step={0.1} class="app-input w-24 text-right font-mono text-sm" />
            </div>
          {/if}
          {#each [
            { label: $t.setting_force_dump, key: "forceDump" },
            { label: $t.setting_no_redirected_pointer, key: "noRedirectedPointer" },
          ] as item}
            <div class="flex items-center justify-between">
              <span class="text-sm">{item.label}</span>
              <button type="button" class="app-toggle {config[item.key as keyof DumperConfig] ? 'active' : ''}" aria-label={item.label} onclick={() => (config[item.key as keyof DumperConfig] as any) = !config[item.key as keyof DumperConfig]}></button>
            </div>
          {/each}
          <div class="flex items-center justify-between">
            <div class="flex flex-col">
              <span class="text-sm">CODM (Call of Duty Mobile)</span>
              <span class="text-xs" style="color: var(--text-secondary);">Enable for CODM's custom v23 metadata layout</span>
            </div>
            <button type="button" class="app-toggle {config.codm ? 'active' : ''}" aria-label="CODM" onclick={() => config.codm = !config.codm}></button>
          </div>
        </div>
      </section>
    </div>

    <!-- Footer -->
    <div class="p-6 pt-4 shrink-0 border-t" style="border-color: var(--card-border);">
      <button type="button" class="app-btn app-btn-primary w-full" onclick={onclose}>Done</button>
    </div>
  </div>
</div>
