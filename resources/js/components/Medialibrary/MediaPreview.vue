<template>
  <embed
    v-if="media && media.mimeType && media.mimeType.includes('pdf')"
    :src="media.previewUrl"
    type="application/pdf"
    class="block h-24 w-full object-cover"
  />
  <img
    v-else
    :src="media.previewUrl"
    :alt="media.fileName"
    class="block h-24 w-full object-cover"
    style="max-height: 100%"
    @error="loadingFailed = true"
  />
  <div style="
    position: relative;
    height: 20px;
  ">
    <a
      v-if="showFullMediaLink"
      style="
        position: absolute;
        z-index: 1;
        left: 50%;
        margin-left: -8px;
      "
      :href="media.previewUrl" target="_blank" class="text-xs text-80 truncate">
      <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor" width="18" height="18" class="inline-block" role="presentation"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z"></path><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M2.458 12C3.732 7.943 7.523 5 12 5c4.478 0 8.268 2.943 9.542 7-1.274 4.057-5.064 7-9.542 7-4.477 0-8.268-2.943-9.542-7z"></path></svg>
    </a>
  </div>
</template>

<script>
export default {
  props: {
    media: {
      type: Object,
      required: true,
    },
    useFallback: {
      type: Boolean,
      default: false,
    },
    showFullMediaLink: {
      type: Boolean,
      default: false,
    },
  },

  data() {
    return {
      loadingFailed: false,
    }
  },

  computed: {
    usePreview() {
      return this.media.previewUrl && !this.loadingFailed && !this.useFallback
    },
  },
}
</script>
