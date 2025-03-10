<script lang="ts">
  import QRCodeItem from './QRCodeItem.svelte'

  const { qrCodes, onHandleQRCode } = $props()

  const handleQRCode = (event: CustomEvent) => {
      const { action, qrCodeId } = event.detail
      onHandleQRCode?.({ action, qrCodeId })
  }
</script>
  
<div class="qr-codes">
  {#each qrCodes as qrCode (qrCode.url)}
    <QRCodeItem {qrCode} onHandleQRCode={handleQRCode}/>
  {/each}
</div>

<style>
.qr-codes {
  position: relative;
}
</style>