<script lang="ts">
  import { invoke } from "@tauri-apps/api/core";
  import { inputRequest, t } from "$lib/stores";
  import { Button } from "$lib/components/ui/button/index.js";
  import { Input } from "$lib/components/ui/input/index.js";
  import { Label } from "$lib/components/ui/label/index.js";
  import * as Dialog from "$lib/components/ui/dialog/index.js";

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

<Dialog.Root open={dumpOpen}>
  <Dialog.Content class="sm:max-w-md">
    <Dialog.Header>
      <Dialog.Title class="flex items-center gap-2.5">
        <div class="size-8 rounded-lg bg-primary/10 flex items-center justify-center ring-1 ring-primary/20">
          <svg xmlns="http://www.w3.org/2000/svg" width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" class="text-primary"><path d="M21 12a9 9 0 0 0-9-9 9.75 9.75 0 0 0-6.74 2.74L3 8"/><path d="M3 3v5h5"/></svg>
        </div>
        {$t.dialog_dump_address_title}
      </Dialog.Title>
      <Dialog.Description>
        {$t.dialog_dump_address_desc}
      </Dialog.Description>
    </Dialog.Header>
    <div class="py-4">
      <Input
        bind:value={dumpAddress}
        placeholder="0x10000"
        class="font-mono"
      />
    </div>
    <Dialog.Footer>
      <Button variant="ghost" onclick={skipDumpAddress}>{$t.dialog_skip}</Button>
      <Button onclick={submitDumpAddress}>OK</Button>
    </Dialog.Footer>
  </Dialog.Content>
</Dialog.Root>

<Dialog.Root open={manualOpen}>
  <Dialog.Content class="sm:max-w-md">
    <Dialog.Header>
      <Dialog.Title class="flex items-center gap-2.5">
        <div class="size-8 rounded-lg bg-primary/10 flex items-center justify-center ring-1 ring-primary/20">
          <svg xmlns="http://www.w3.org/2000/svg" width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" class="text-primary"><polyline points="16 18 22 12 16 6"/><polyline points="8 6 2 12 8 18"/></svg>
        </div>
        {$t.dialog_manual_title}
      </Dialog.Title>
      <Dialog.Description>
        {$t.dialog_manual_desc}
      </Dialog.Description>
    </Dialog.Header>
    <div class="py-4 space-y-4">
      <div class="space-y-2">
        <Label class="text-xs uppercase tracking-widest">{$t.setting_code_registration}</Label>
        <Input
          bind:value={codeReg}
          placeholder="0x654aef0"
          class="font-mono {codeReg && !isValidHex(codeReg) ? 'border-destructive ring-destructive/20 ring-2' : ''}"
        />
      </div>
      <div class="space-y-2">
        <Label class="text-xs uppercase tracking-widest">{$t.setting_metadata_registration}</Label>
        <Input
          bind:value={metaReg}
          placeholder="0x66c4998"
          class="font-mono {metaReg && !isValidHex(metaReg) ? 'border-destructive ring-destructive/20 ring-2' : ''}"
        />
      </div>
    </div>
    <Dialog.Footer>
      <Button variant="ghost" onclick={cancelManual}>Cancel</Button>
      <Button disabled={!isValidHex(codeReg) || !isValidHex(metaReg)} onclick={submitManualAddresses}>OK</Button>
    </Dialog.Footer>
  </Dialog.Content>
</Dialog.Root>
