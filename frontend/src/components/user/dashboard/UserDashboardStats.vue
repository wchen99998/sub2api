<template>
  <!-- Row 1: Core Stats -->
  <div class="grid grid-cols-2 gap-4 lg:grid-cols-4">
    <!-- Balance -->
    <div v-if="!isSimple" class="rounded-mica-lg bg-white/70 dark:bg-white/[0.05] backdrop-blur-xl border border-black/[0.06] dark:border-white/[0.08] p-4">
      <p class="text-mica-caption uppercase text-mica-text-secondary dark:text-mica-text-secondary-dark">{{ t('dashboard.balance') }}</p>
      <p class="mt-1.5 text-[22px] font-semibold tracking-tight text-status-green dark:text-status-green-dark">${{ formatBalance(balance) }}</p>
      <p class="mt-0.5 text-mica-caption text-mica-text-tertiary dark:text-mica-text-tertiary-dark">{{ t('common.available') }}</p>
    </div>

    <!-- API Keys -->
    <div class="rounded-mica-lg bg-white/70 dark:bg-white/[0.05] backdrop-blur-xl border border-black/[0.06] dark:border-white/[0.08] p-4">
      <p class="text-mica-caption uppercase text-mica-text-secondary dark:text-mica-text-secondary-dark">{{ t('dashboard.apiKeys') }}</p>
      <p class="mt-1.5 text-[22px] font-semibold tracking-tight text-mica-text-primary dark:text-mica-text-primary-dark">{{ stats?.total_api_keys || 0 }}</p>
      <p class="mt-0.5 text-mica-caption text-mica-text-tertiary dark:text-mica-text-tertiary-dark"><span class="text-status-green dark:text-status-green-dark">{{ stats?.active_api_keys || 0 }}</span> {{ t('common.active') }}</p>
    </div>

    <!-- Today Requests -->
    <div class="rounded-mica-lg bg-white/70 dark:bg-white/[0.05] backdrop-blur-xl border border-black/[0.06] dark:border-white/[0.08] p-4">
      <p class="text-mica-caption uppercase text-mica-text-secondary dark:text-mica-text-secondary-dark">{{ t('dashboard.todayRequests') }}</p>
      <p class="mt-1.5 text-[22px] font-semibold tracking-tight text-mica-text-primary dark:text-mica-text-primary-dark">{{ stats?.today_requests || 0 }}</p>
      <p class="mt-0.5 text-mica-caption text-mica-text-tertiary dark:text-mica-text-tertiary-dark">{{ t('common.total') }}: {{ formatNumber(stats?.total_requests || 0) }}</p>
    </div>

    <!-- Today Cost -->
    <div class="rounded-mica-lg bg-white/70 dark:bg-white/[0.05] backdrop-blur-xl border border-black/[0.06] dark:border-white/[0.08] p-4">
      <p class="text-mica-caption uppercase text-mica-text-secondary dark:text-mica-text-secondary-dark">{{ t('dashboard.todayCost') }}</p>
      <p class="mt-1.5 text-[22px] font-semibold tracking-tight text-mica-text-primary dark:text-mica-text-primary-dark">
        <span :title="t('dashboard.actual')">${{ formatCost(stats?.today_actual_cost || 0) }}</span>
        <span class="text-sm font-normal text-mica-text-tertiary dark:text-mica-text-tertiary-dark" :title="t('dashboard.standard')"> / ${{ formatCost(stats?.today_cost || 0) }}</span>
      </p>
      <p class="mt-0.5 text-mica-caption text-mica-text-tertiary dark:text-mica-text-tertiary-dark">
        <span>{{ t('common.total') }}: </span>
        <span :title="t('dashboard.actual')">${{ formatCost(stats?.total_actual_cost || 0) }}</span>
        <span :title="t('dashboard.standard')"> / ${{ formatCost(stats?.total_cost || 0) }}</span>
      </p>
    </div>
  </div>

  <!-- Row 2: Token Stats -->
  <div class="grid grid-cols-2 gap-4 lg:grid-cols-4">
    <!-- Today Tokens -->
    <div class="rounded-mica-lg bg-white/70 dark:bg-white/[0.05] backdrop-blur-xl border border-black/[0.06] dark:border-white/[0.08] p-4">
      <p class="text-mica-caption uppercase text-mica-text-secondary dark:text-mica-text-secondary-dark">{{ t('dashboard.todayTokens') }}</p>
      <p class="mt-1.5 text-[22px] font-semibold tracking-tight text-mica-text-primary dark:text-mica-text-primary-dark">{{ formatTokens(stats?.today_tokens || 0) }}</p>
      <p class="mt-0.5 text-mica-caption text-mica-text-tertiary dark:text-mica-text-tertiary-dark">{{ t('dashboard.input') }}: {{ formatTokens(stats?.today_input_tokens || 0) }} / {{ t('dashboard.output') }}: {{ formatTokens(stats?.today_output_tokens || 0) }}</p>
    </div>

    <!-- Total Tokens -->
    <div class="rounded-mica-lg bg-white/70 dark:bg-white/[0.05] backdrop-blur-xl border border-black/[0.06] dark:border-white/[0.08] p-4">
      <p class="text-mica-caption uppercase text-mica-text-secondary dark:text-mica-text-secondary-dark">{{ t('dashboard.totalTokens') }}</p>
      <p class="mt-1.5 text-[22px] font-semibold tracking-tight text-mica-text-primary dark:text-mica-text-primary-dark">{{ formatTokens(stats?.total_tokens || 0) }}</p>
      <p class="mt-0.5 text-mica-caption text-mica-text-tertiary dark:text-mica-text-tertiary-dark">{{ t('dashboard.input') }}: {{ formatTokens(stats?.total_input_tokens || 0) }} / {{ t('dashboard.output') }}: {{ formatTokens(stats?.total_output_tokens || 0) }}</p>
    </div>

    <!-- Performance (RPM/TPM) -->
    <div class="rounded-mica-lg bg-white/70 dark:bg-white/[0.05] backdrop-blur-xl border border-black/[0.06] dark:border-white/[0.08] p-4">
      <p class="text-mica-caption uppercase text-mica-text-secondary dark:text-mica-text-secondary-dark">{{ t('dashboard.performance') }}</p>
      <div class="mt-1.5 flex items-baseline gap-1.5">
        <p class="text-[22px] font-semibold tracking-tight text-mica-text-primary dark:text-mica-text-primary-dark">{{ formatTokens(stats?.rpm || 0) }}</p>
        <span class="text-mica-caption text-mica-text-tertiary dark:text-mica-text-tertiary-dark">RPM</span>
      </div>
      <div class="mt-0.5 flex items-baseline gap-1.5">
        <p class="text-sm font-semibold text-mica-text-secondary dark:text-mica-text-secondary-dark">{{ formatTokens(stats?.tpm || 0) }}</p>
        <span class="text-mica-caption text-mica-text-tertiary dark:text-mica-text-tertiary-dark">TPM</span>
      </div>
    </div>

    <!-- Avg Response Time -->
    <div class="rounded-mica-lg bg-white/70 dark:bg-white/[0.05] backdrop-blur-xl border border-black/[0.06] dark:border-white/[0.08] p-4">
      <p class="text-mica-caption uppercase text-mica-text-secondary dark:text-mica-text-secondary-dark">{{ t('dashboard.avgResponse') }}</p>
      <p class="mt-1.5 text-[22px] font-semibold tracking-tight text-mica-text-primary dark:text-mica-text-primary-dark">{{ formatDuration(stats?.average_duration_ms || 0) }}</p>
      <p class="mt-0.5 text-mica-caption text-mica-text-tertiary dark:text-mica-text-tertiary-dark">{{ t('dashboard.averageTime') }}</p>
    </div>
  </div>
</template>

<script setup lang="ts">
import { useI18n } from 'vue-i18n'
import type { UserDashboardStats as UserStatsType } from '@/api/usage'

defineProps<{
  stats: UserStatsType
  balance: number
  isSimple: boolean
}>()
const { t } = useI18n()

const formatBalance = (b: number) =>
  new Intl.NumberFormat('en-US', {
    minimumFractionDigits: 2,
    maximumFractionDigits: 2
  }).format(b)

const formatNumber = (n: number) => n.toLocaleString()
const formatCost = (c: number) => c.toFixed(4)
const formatTokens = (t: number) => {
  if (t >= 1_000_000) return `${(t / 1_000_000).toFixed(1)}M`
  if (t >= 1000) return `${(t / 1000).toFixed(1)}K`
  return t.toString()
}
const formatDuration = (ms: number) => ms >= 1000 ? `${(ms / 1000).toFixed(2)}s` : `${ms.toFixed(0)}ms`
</script>
