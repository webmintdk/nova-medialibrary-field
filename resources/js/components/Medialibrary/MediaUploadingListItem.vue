<template>
  <div
    v-tooltip="tooltip"
    class="bg-50 group relative flex h-16 w-16 overflow-hidden rounded shadow"
    :class="{ 'shadow-danger': media.uploadingFailed }"
  >
    <loader v-if="previewLoading" class="text-60" :width="30" />
    <embed
      v-if="media && (media.mimeType && media.mimeType.includes('pdf')) || (media.fileName && media.fileName.endsWith('.pdf'))"
      :src="media.previewUrl"
      type="application/pdf"
      class="block h-24 w-24 object-cover"
    />
    <img
      v-else
      :src="preview"
      :alt="media.previewUrl"
      class="h-16 w-16 object-cover shadow"
      :class="{ 'group-hover:opacity-75': !media.uploading }"
    />

    <div v-if="media.uploading" class="bg-overlay absolute inset-0 h-full w-full">
      <svg class="progress-ring" viewBox="0 0 100 100" xmlns="http://www.w3.org/2000/svg">
        <circle
          :r="progressCircleRadius"
          :style="progressCircleStyle"
          cx="50"
          cy="50"
          class="progress-ring__circle"
          stroke="white"
          stroke-width="4"
          fill="transparent"
        />
      </svg>
    </div>

    <div v-else class="bg-overlay absolute inset-0 hidden group-hover:block">
      <div class="flex h-full items-center justify-center">
        <button
          type="button"
          class="hover:text-danger flex text-white focus:outline-none"
          @click="media.remove()"
          dusk="nova-media-uploading-list-item-media-remove-button"
        >
          <icon type="delete" view-box="0 0 20 20" width="20" height="20" />
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import { tooltip } from './Utils'

export default {
  props: {
    media: {
      type: Object,
      required: true,
    },
  },

  data() {
    return {
      preview: null,
      previewLoading: false,
    }
  },

  computed: {
    sizeInKb() {
      return (this.media.size / 1024).toFixed(2)
    },

    tooltipHtml() {
      return `${this.media.fileName}, ${this.sizeInKb} KB`
    },

    errorsHtml() {
      return this.media.validationErrors
        .get('file')
        .map((error) => `<span class="text-danger">${error}</span>`)
        .join('<br>')
    },

    tooltip() {
      const invalid = this.media.validationErrors.has('file')

      return tooltip(`${this.tooltipHtml}<br>${this.errorsHtml}`, {
        classes: `bg-white p-2 rounded border border-${invalid ? 'danger' : '50'} shadow text-sm leading-normal`,
      })
    },

    progressCircleRadius() {
      return 46
    },

    progressCircleStyle() {
      const circumference = this.progressCircleRadius * 2 * Math.PI

      const offset = circumference - (this.media.uploadingProgress / 100) * circumference

      return {
        strokeDasharray: `${circumference} ${circumference}`,
        strokeDashoffset: `${offset}`,
      }
    },
  },

  created() {
    this.loadPreview()
  },

  methods: {
    loadPreview() {
      if (this.media.isImage) {
        this.preview = this.media.previewUrl
      } else {
        return
      }

      if (this.preview) {
        return
      }

      this.previewLoading = true

      const fileReader = new FileReader()

      fileReader.onload = () =>
        setTimeout(() => {
          this.preview = fileReader.result
          this.previewLoading = false
        }, 200)

      fileReader.readAsDataURL(this.media.file)
    },
  },
}
</script>

<style lang="scss" scoped>
.progress-ring {
  &__circle {
    transition: 0.25s stroke-dashoffset;
    transform: rotate(-90deg);
    transform-origin: 50% 50%;
  }
}
</style>
