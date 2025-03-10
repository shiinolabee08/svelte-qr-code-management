<script lang="ts">
  import { v4 as uuidv4 } from 'uuid'
  import QRCode from '@castlenine/svelte-qrcode'

  interface QRCodeRecord {
    id: string;
    url: string;
    name: string;
    description: string;
    color?: string;
    downloadUrl?: string;
  }
  
  let formData = $state({
    id: '',
    url: '',
    name: '',
    description: '',
    color: '',
    downloadUrl: ''
  } as QRCodeRecord)

  let isValidUrl: boolean = $state(false)
  let isColorTouched: boolean = $state(false)
  let responseMessage: string = $state('')

  const { onAddQRCode } = $props()

  const newQRCode = $derived(formData)

  const basicColors = ['black', 'red', 'green', 'blue', 'yellow', 'orange']

  const handleSubmit = async () => {
    if (newQRCode.name.trim().length) {
      const updatedQRCode = {
          ...newQRCode,
          id: uuidv4(),
          approved: false,
          isUpdated: false
      };

      const { url, name, description, color, downloadUrl } = formData

      const postData = {
        url,
        name,
        description,
        color,
        qrCodeSvg: downloadUrl,
      }

      const res = await fetch("https://script.google.com/macros/s/AKfycbz_HGB5mxTVepygfKsJRIp1sYZbhcH8vwMuvZGxUuC11D-QiRA800ps_3OZvL1dAbEb/exec", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(postData),
      })

      const data = await res.json()

      responseMessage = data.status === 'success' ? 'QR Code record successfully recorded to Google Sheets' : 'Error sending form submission to server.'

      onAddQRCode?.(updatedQRCode)
      clearFormFields()
    }
  }

  const clearFormFields = () => {
    formData = {
        id: '',
        url: '',
        name: '',
        description: '',
    }
  }

  const handleIsValidURL = (url: string): boolean => {
    const pattern = /^(https?:\/\/)?([\w-]+\.)+[\w-]+(\/[\w-./?%&=]*)?$/i;
    return pattern.test(url);
  }

  const handleDownloadUrlGenerated = (url = '') => {
    formData.downloadUrl = url;
  };
</script>

<form class="max-w-md mx-auto m-10 text-white" onsubmit={e => { e.preventDefault(); handleSubmit(); }}>
  <div class="max-w-sm p-6 bg-white border border-gray-200 rounded-lg shadow-sm dark:bg-gray-800 dark:border-gray-700">
    <h2 class="text-white mb-10">Generate new QR Code</h2>

    <div class="relative z-0 w-full mb-5 group">
      <input 
        type="text"
        name="url"
        bind:value={formData.url}
        onchange={(e) => isValidUrl = handleIsValidURL(e.currentTarget.value)}
        class="block py-2.5 px-0 w-full text-sm bg-transparent border-0 border-b-2 border-gray-300 appearance-none dark:border-gray-600 dark:focus:border-blue-500 focus:outline-none focus:ring-0 focus:border-blue-600 peer"
        placeholder=""
        required />
      <label 
        for="url" 
        class="peer-focus:font-medium absolute text-sm text-gray-500 dark:text-gray-400 duration-300 transform -translate-y-6 scale-75 top-3 -z-10 origin-[0] peer-focus:start-0 rtl:peer-focus:translate-x-1/4 rtl:peer-focus:left-auto peer-focus:text-blue-600 peer-focus:dark:text-blue-500 peer-placeholder-shown:scale-100 peer-placeholder-shown:translate-y-0 peer-focus:scale-75 peer-focus:-translate-y-6">
          URL(e.g https://samplelink.com.au/landing-page)
      </label>
      {#if !isValidUrl && formData.url.length}
        <p class="mt-2 text-sm text-red-600 dark:text-red-500"><span class="font-medium">Oops!</span> URL is invalid.</p>
      {/if}
    </div>
    <div class="relative z-0 w-full mb-5 group">
        <input 
          type="text" 
          name="qrcode_name"
          bind:value={formData.name}
          class="block py-2.5 px-0 w-full text-sm bg-transparent border-0 border-b-2 border-gray-300 appearance-none dark:border-gray-600 dark:focus:border-blue-500 focus:outline-none focus:ring-0 focus:border-blue-600 peer" 
          placeholder=""
          required />
        <label 
          for="qrcode_name"
          class="peer-focus:font-medium absolute text-sm text-gray-500 dark:text-gray-400 duration-300 transform -translate-y-6 scale-75 top-3 -z-10 origin-[0] peer-focus:start-0 rtl:peer-focus:translate-x-1/4 peer-focus:text-blue-600 peer-focus:dark:text-blue-500 peer-placeholder-shown:scale-100 peer-placeholder-shown:translate-y-0 peer-focus:scale-75 peer-focus:-translate-y-6">
            Name
        </label>
    </div>
    <div class="relative z-0 w-full mb-5 group">
      <textarea 
        name="description"
        bind:value={formData.description}
        class="block py-2.5 px-0 w-full text-sm bg-transparent border-0 border-b-2 border-gray-300 appearance-none dark:border-gray-600 dark:focus:border-blue-500 focus:outline-none focus:ring-0 focus:border-blue-600 peer" 
        required>
      </textarea>
      <label 
        for="description" 
        class="peer-focus:font-medium absolute text-sm text-gray-500 dark:text-gray-400 duration-300 transform -translate-y-6 scale-75 top-3 -z-10 origin-[0] peer-focus:start-0 rtl:peer-focus:translate-x-1/4 peer-focus:text-blue-600 peer-focus:dark:text-blue-500 peer-placeholder-shown:scale-100 peer-placeholder-shown:translate-y-0 peer-focus:scale-75 peer-focus:-translate-y-6">
          Description
      </label>
    </div>
    <div class="relative z-0 w-full mb-5 group">
      <select 
        name="color"
        bind:value={formData.color}
        onchange={(e) => isColorTouched = true}
        class="block py-2.5 px-0 w-full text-sm bg-transparent border-0 border-b-2 border-gray-300 appearance-none dark:border-gray-600 dark:focus:border-blue-500 focus:outline-none focus:ring-0 focus:border-blue-600 peer"
        required>
        {#each basicColors as color}
          {#if color === 'black'}
            <option value={color}>Default({color})</option>
          {:else}
            <option value={color}>{color}</option>
          {/if}
        {/each}
      </select>
      <label 
        for="basic_color"
        class="peer-focus:font-medium absolute text-sm text-gray-500 dark:text-gray-400 duration-300 transform -translate-y-6 scale-75 top-3 -z-10 origin-[0] peer-focus:start-0 rtl:peer-focus:translate-x-1/4 peer-focus:text-blue-600 peer-focus:dark:text-blue-500 peer-placeholder-shown:scale-100 peer-placeholder-shown:translate-y-0 peer-focus:scale-75 peer-focus:-translate-y-6">
          Basic color
      </label>
    </div>

    {#if formData.url.length > 0 && isValidUrl && isColorTouched}
      <div class="preview my-10">
        <h4 class="mb-5">Preview:</h4>
        <QRCode 
          data={formData.url} 
          backgroundColor={formData.color} 
          modulesColor="#ffffff"
          haveGappedModules
          isJoin
          isResponsive={true}
          dispatchDownloadUrl
          on:downloadUrlGenerated={(event) => handleDownloadUrlGenerated(event.detail.url)}
        />
      </div>
    {/if}
    
    <button 
      type="submit" 
      class="text-white bg-blue-700 hover:bg-blue-800 focus:ring-4 focus:outline-none focus:ring-blue-300 font-medium rounded-lg text-sm w-full sm:w-auto px-5 py-2.5 text-center dark:bg-blue-600 dark:hover:bg-blue-700 dark:focus:ring-blue-800">
        Save &amp; Submit
    </button>

    {#if formData.downloadUrl}
      <a class="mx-2" href={formData.downloadUrl} download="QR-code-{formData.name}.svg" target="_blank">Download QR Code</a>
    {/if}

    {#if responseMessage}
      <p>{responseMessage}</p>
    {/if}
  </div>
  
</form>

