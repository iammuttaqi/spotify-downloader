<script setup lang="ts">
import { ref } from 'vue'

const getTrackIdFromSpotifyUrl = (url: string) => {
  const regex = /track\/([A-Za-z0-9]+)/
  const match = url.match(regex)
  if (match) {
    const trackId = match[1]
    return 'https://api.spotifydown.com/download/' + trackId
  }
  return null
}

const link = ref()
const processing = ref(false)

const download = async () => {
  processing.value = true
  const url: string | null = getTrackIdFromSpotifyUrl(link.value)
  const data = ref()
  const error = ref()

  if (url) {
    await fetch(url)
      .then((res) => res.json())
      .then((json) => (data.value = json))
      .catch((err) => (error.value = err))

    if (data.value) {
      processing.value = false
      window.open(data.value.link, '_blank')
    }
  } else {
    processing.value = false
    alert('invalid link')
  }
}
</script>

<template>
  <!-- <form @submit.prevent="download">
    <input type="text" placeholder="Link" v-model="link" />
    <button type="submit">Download</button>
  </form> -->

  <main class="w-full mx-auto p-6">
    <div
      class="mt-7 bg-white border border-gray-200 rounded-xl shadow-sm dark:bg-gray-800 dark:border-gray-700"
    >
      <div class="p-4 sm:p-7">
        <div class="text-center">
          <h1 class="block text-2xl font-bold text-gray-800 dark:text-white">Spotify Downloader</h1>
          <p class="mt-2 text-sm text-gray-600 dark:text-gray-400">
            Link format:
            <span class="font-bold"
              >https://open.spotify.com/track/4cOdK2wGLETKBW3PvgPWqT?si=e8fc91a086614577</span
            >
          </p>
        </div>

        <div class="mt-5">
          <!-- Form -->
          <form @submit.prevent="download">
            <div class="grid gap-y-4">
              <!-- Form Group -->
              <div>
                <label for="link" class="block text-sm mb-2 dark:text-white">Link</label>
                <div class="relative">
                  <input
                    type="url"
                    id="link"
                    name="link"
                    class="py-3 px-4 block w-full border border-gray-200 rounded-md text-sm dark:bg-gray-800 dark:border-gray-500 dark:text-gray-400"
                    required
                    v-model="link"
                    placeholder="Link"
                    aria-describedby="link-error"
                  />
                  <!-- <div
                    class="hidden absolute inset-y-0 right-0 flex items-center pointer-events-none pr-3"
                  >
                    <svg
                      class="h-5 w-5 text-red-500"
                      width="16"
                      height="16"
                      fill="currentColor"
                      viewBox="0 0 16 16"
                      aria-hidden="true"
                    >
                      <path
                        d="M16 8A8 8 0 1 1 0 8a8 8 0 0 1 16 0zM8 4a.905.905 0 0 0-.9.995l.35 3.507a.552.552 0 0 0 1.1 0l.35-3.507A.905.905 0 0 0 8 4zm.002 6a1 1 0 1 0 0 2 1 1 0 0 0 0-2z"
                      />
                    </svg>
                  </div> -->
                </div>
                <p class="hidden text-xs text-red-600 mt-2" id="email-error">
                  Please include a spotify link so we can get back to you
                </p>
              </div>
              <!-- End Form Group -->

              <button
                type="submit"
                class="py-3 px-4 inline-flex justify-center items-center gap-2 rounded-md border border-transparent font-semibold bg-blue-500 text-white hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2 transition-all text-sm dark:focus:ring-offset-gray-800 disabled:cursor-not-allowed disabled:bg-blue-400"
                :disabled="processing"
              >
                <span
                  v-if="processing"
                  class="animate-spin inline-block w-4 h-4 border-[3px] border-current border-t-transparent text-white rounded-full"
                  role="status"
                  aria-label="loading"
                ></span>
                Download
              </button>
            </div>
          </form>
          <!-- End Form -->
        </div>
      </div>
    </div>
  </main>
</template>
