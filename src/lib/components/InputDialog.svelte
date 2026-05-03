<script lang="ts">
  import { invoke } from "@tauri-apps/api/core";
  import { inputRequest, t } from "$lib/stores";

  let dumpAddress = $state("");
  let codeReg = $state("");
  let metaReg = $state("");

  function isValidHex(s: string): boolean {
    if (!s.trim()) return false;
    const stripped = s.trim().replace(/^0[xX]/, "");
    return stripped.length > 0 && /^[0-9a-fA-F]+$/.test(stripped);
  }

  async function submitDumpAddress() {
    await invoke("submit_input", { response: dumpAddress || "0" });
    inputRequest.set(null);
    dumpAddress = "";
  }

  async function skipDumpAddress() {
    await invoke("submit_input", { response: "0" });
    inputRequest.set(null);
    dumpAddress = "";
  }

  async function submitManualAddresses() {
    await invoke("submit_input", { response: `${codeReg},${metaReg}` });
    inputRequest.set(null);
    codeReg = "";
    metaReg = "";
  }

  async function cancelManual() {
    await invoke("submit_input", { response: "" });
    inputRequest.set(null);
    codeReg = "";
    metaReg = "";
  }

  let dumpOpen = $derived($inputRequest === "dump_address");
  let manualOpen = $derived($inputRequest === "manual_addresses");
</script>

<!-- Dump Address Dialog -->
{#if dumpOpen}
  <div class="app-dialog">
    <button type="button" class="app-backdrop cursor-default" aria-label="Close dialog" onclick={skipDumpAddress}></button>
    <div class="app-card w-full max-w-md mx-4 p-6 space-y-5 shadow-2xl relative z-10">
      <div class="space-y-2">
        <div class="flex items-center gap-2.5">
          <div class="size-8 rounded-lg flex items-center justify-center" style="background: var(--accent-soft); box-shadow: inset 0 0 0 1px var(--accent-ring);">
            <svg xmlns="http://www.w3.org/2000/svg" width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" style="color: var(--accent);"><path d="M21 12a9 9 0 0 0-9-9 9.75 9.75 0 0 0-6.74 2.74L3 8"/><path d="M3 3v5h5"/></svg>
          </div>
          <h3 class="text-lg font-semibold">{$t.dialog_dump_address_title}</h3>
        </div>
        <p class="text-sm" style="color: var(--text-secondary);">{$t.dialog_dump_address_desc}</p>
      </div>
      <input
        type="text"
        bind:value={dumpAddress}
        placeholder="0x10000"
        class="app-input font-mono"
      />
      <div class="flex justify-end gap-2">
        <button type="button" class="app-btn app-btn-ghost" onclick={skipDumpAddress}>{$t.dialog_skip}</button>
        <button type="button" class="app-btn app-btn-primary" onclick={submitDumpAddress}>OK</button>
      </div>
    </div>
  </div>
{/if}

<!-- Manual Addresses Dialog -->
{#if manualOpen}
  <div class="app-dialog">
    <button type="button" class="app-backdrop cursor-default" aria-label="Close dialog" onclick={cancelManual}></button>
    <div class="app-card w-full max-w-md mx-4 p-6 space-y-5 shadow-2xl relative z-10">
      <div class="space-y-2">
        <div class="flex items-center gap-2.5">
          <div class="size-8 rounded-lg flex items-center justify-center" style="background: var(--accent-soft); box-shadow: inset 0 0 0 1px var(--accent-ring);">
            <svg xmlns="http://www.w3.org/2000/svg" width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" style="color: var(--accent);"><polyline points="16 18 22 12 16 6"/><polyline points="8 6 2 12 8 18"/></svg>
          </div>
          <h3 class="text-lg font-semibold">{$t.dialog_manual_title}</h3>
        </div>
        <p class="text-sm" style="color: var(--text-secondary);">{$t.dialog_manual_desc}</p>
      </div>
      <div class="space-y-4">
        <div class="space-y-2">
          <label for="input-code-reg" class="text-xs uppercase tracking-widest font-medium" style="color: var(--text-secondary);">{$t.setting_code_registration}</label>
          <input
            id="input-code-reg"
            type="text"
            bind:value={codeReg}
            placeholder="0x654aef0"
            class="app-input font-mono"
            style={codeReg && !isValidHex(codeReg) ? 'border-color: var(--error); box-shadow: 0 0 0 2px var(--error-ring);' : ''}
          />
        </div>
        <div class="space-y-2">
          <label for="input-meta-reg" class="text-xs uppercase tracking-widest font-medium" style="color: var(--text-secondary);">{$t.setting_metadata_registration}</label>
          <input
            id="input-meta-reg"
            type="text"
            bind:value={metaReg}
            placeholder="0x66c4998"
            class="app-input font-mono"
            style={metaReg && !isValidHex(metaReg) ? 'border-color: var(--error); box-shadow: 0 0 0 2px var(--error-ring);' : ''}
          />
        </div>
      </div>
      <div class="flex justify-end gap-2">
        <button type="button" class="app-btn app-btn-ghost" onclick={cancelManual}>Cancel</button>
        <button type="button" class="app-btn app-btn-primary" disabled={!isValidHex(codeReg) || !isValidHex(metaReg)} onclick={submitManualAddresses}>OK</button>
      </div>
    </div>
  </div>
{/if}
