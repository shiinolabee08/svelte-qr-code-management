<script lang="ts">
  import QRCodeList from '$lib/components/QRCodeList.svelte'
  import QRCodeForm from '$lib/components/QRCodeForm.svelte'

  let qrCodes: any = $state([]);

  const handleQRCodeAction = (e: CustomEvent) => {

    const { action, qrCodeId } = e.detail;
      
    qrCodes = qrCodes.filter((item: any) => item.id !== qrCodeId)
  }

  const handleAddQRCodeAction = (e: CustomEvent) => {
    const newQRCode = e.detail
    qrCodes = [...qrCodes, newQRCode]
  }
</script>

<main class="container">
  <QRCodeForm onAddQRCode={handleAddQRCodeAction}/>
</main>

<style>
  .container{
    margin: 10px auto;
    max-width: 768px;
    background-color: '#f4f4f4';
    padding: 0px 20px;
  }
</style>