<script setup>
import { ref, computed } from 'vue'
import { useClipboard } from '@vueuse/core'

const encodedResult = ref('')
const { copy, copied, isSupported } = useClipboard()
const decodeInput = ref('')

const handleFileUpload = (event) => {
  const file = event.target.files[0]
  if (!file) return

  const reader = new FileReader()

  reader.onload = () => {
    encodedResult.value = reader.result // includes Data URI (data:image/png;base64 + the actual b64 stuff)
  }

  reader.onerror = (error) => {
    console.error('Error: ', error)
  }

  reader.readAsDataURL(file)
}

// Decoding. Does not do anything if the textbox is empty
const hasContentToDecode = computed(() => {
  return decodeInput.value && decodeInput.value.length > 0
})
</script>

<template>
  <main>
    <h1>Base64 Shenanigans</h1>
    <section>
      <h2>Image -> Base64</h2>
      <input type="file" accept="image/*" @change="handleFileUpload" />

      <div v-if="encodedResult">
        <textarea
          id="encoded-output"
          v-model="encodedResult"
          rows="20"
          readonly
          style="width: 100%"
        ></textarea>
        <button @click="copy(encodedResult)">
          <span v-if="!copied"> Copy the output </span>
          <span v-else>Copied the output</span>
        </button>
      </div>
    </section>

    <section>
      <h2>Base64 -> Image</h2>
      <textarea v-model="decodeInput" rows="20" style="width: 100%"></textarea>
      <div v-if="hasContentToDecode">
        <h1>Decoded:</h1>
        <img :src="decodeInput" alt="Invalid Data" style="max-width: 100%" />
      </div>
    </section>
  </main>
</template>

<style scoped></style>
