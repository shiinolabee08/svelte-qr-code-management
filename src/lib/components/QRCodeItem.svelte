<script lang="ts">
  import QRCode from '@castlenine/svelte-qrcode'

  const { onHandleQRCode, qrCode } = $props()

  // const qrCodeActions = ['approve', 'reject']

  const handleQRCodeAction = (action: string, qrCodeId: string) => {
    onHandleQRCode?.({ action, qrCodeId })
  }

  // $derived(getQRCodeStatusDisabled = (action: string) => {
  //   return qrCode.approved && action === 'approve';
  // })
</script>

<div class="relative overflow-x-auto">
  <table class="w-full text-sm text-left rtl:text-right text-gray-500 dark:text-gray-400">
      <thead class="text-xs text-gray-700 uppercase bg-gray-50 dark:bg-gray-700 dark:text-gray-400">
          <tr>
              <th scope="col" class="px-6 py-3">
                QR Code URL
              </th>
              <th scope="col" class="px-6 py-3">
                Name
              </th>
              <th scope="col" class="px-6 py-3">
                Description
              </th>
              <th scope="col" class="px-6 py-3">
                Comments
              </th>
              <th scope="col" class="px-6 py-3">
                Is Approved?
              </th>
              <th scope="col" class="px-6 py-3">
                Actions
              </th>
          </tr>
      </thead>
      <tbody>
          <tr class="bg-white border-b dark:bg-gray-800 dark:border-gray-700 border-gray-200">
            <th scope="row" class="px-6 py-4 font-medium text-gray-900 whitespace-nowrap dark:text-white">
              {qrCode.url}
            </th>
            <td class="px-6 py-4">
              {qrCode.name}
            </td>
            <td class="px-6 py-4">
              {qrCode.description}
            </td>
            <td class="px-6 py-4">
              {qrCode.comments}
            </td>
            <td class="px-6 py-4">
              {qrCode.is_approved}
            </td>
            <td>
              <div class="actions">
                <!-- {#each qrCodeActions as action}
                  <button class={action} disabled={getReviewStatusDisabled(action.toLowerCase())} on:click={handleReviewAction(action.toLowerCase(), review.id)}>{action}</button>
                {/each} -->
              </div>
            </td>
          </tr>
      </tbody>
  </table>
</div>
