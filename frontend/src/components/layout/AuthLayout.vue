<template>
  <div class="relative flex min-h-screen items-center justify-center bg-canvas dark:bg-canvas-dark p-4">
    <div class="relative z-10 w-full max-w-md">
      <div class="mb-8 text-center">
        <template v-if="settingsLoaded">
          <div class="mb-4 inline-flex h-14 w-14 items-center justify-center overflow-hidden rounded-mica-lg">
            <img :src="siteLogo || '/logo.png'" alt="Logo" class="h-full w-full object-contain" />
          </div>
          <h1 class="text-mica-title1 text-mica-text-primary dark:text-mica-text-primary-dark mb-1">
            {{ siteName }}
          </h1>
          <p class="text-mica-subhead text-mica-text-secondary dark:text-mica-text-secondary-dark">
            {{ siteSubtitle }}
          </p>
        </template>
      </div>
      <div class="rounded-mica-lg bg-white/70 dark:bg-white/[0.05] backdrop-blur-xl border border-black/[0.06] dark:border-white/[0.08] p-8">
        <slot />
      </div>
      <div class="mt-6 text-center text-mica-subhead">
        <slot name="footer" />
      </div>
      <div class="mt-8 text-center text-mica-caption text-mica-text-tertiary dark:text-mica-text-tertiary-dark">
        &copy; {{ currentYear }} {{ siteName }}
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, onMounted } from 'vue'
import { useAppStore } from '@/stores'
import { sanitizeUrl } from '@/utils/url'

const appStore = useAppStore()

const siteName = computed(() => appStore.siteName || 'Sub2API')
const siteLogo = computed(() => sanitizeUrl(appStore.siteLogo || '', { allowRelative: true, allowDataUrl: true }))
const siteSubtitle = computed(() => appStore.cachedPublicSettings?.site_subtitle || 'Subscription to API Conversion Platform')
const settingsLoaded = computed(() => appStore.publicSettingsLoaded)

const currentYear = computed(() => new Date().getFullYear())

onMounted(() => {
  appStore.fetchPublicSettings()
})
</script>
