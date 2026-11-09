<template>
  <div class="relative flex min-h-screen items-center justify-center overflow-hidden px-4">
    <SpaceBackground />
    <div class="nf-glow" aria-hidden="true"></div>

    <div class="relative z-10 w-full max-w-md text-center">
      <div class="mb-8">
        <div class="relative inline-block">
          <span class="nf-number">404</span>
          <div class="absolute inset-0 flex items-center justify-center">
            <div class="nf-badge">
              <Icon name="exclamationTriangle" size="lg" class="text-white" />
            </div>
          </div>
        </div>
      </div>

      <div class="mb-8">
        <h1 class="mb-3 text-2xl font-bold text-white">{{ t('errors.pageNotFound') }}</h1>
        <p class="text-sm text-dark-400">你访问的页面不存在，或已经被移动。</p>
      </div>

      <div class="flex flex-col justify-center gap-3 sm:flex-row">
        <button class="btn btn-secondary" @click="goBack">
          <Icon name="arrowLeft" size="md" class="mr-2" />
          返回上一页
        </button>
        <router-link to="/dashboard" class="btn btn-primary">
          <Icon name="home" size="md" class="mr-2" />
          进入控制台
        </router-link>
      </div>

      <p class="mt-8 text-sm text-dark-500">
        需要帮助？
        <a href="mailto:support@airc.fun" class="nf-link">support@airc.fun</a>
      </p>
    </div>
  </div>
</template>

<script setup lang="ts">
import { useI18n } from 'vue-i18n'
import { useRouter } from 'vue-router'
import Icon from '@/components/icons/Icon.vue'
import SpaceBackground from '@/components/common/SpaceBackground.vue'

const { t } = useI18n()
const router = useRouter()

function goBack(): void {
  router.back()
}
</script>

<style scoped>
.nf-glow {
  position: fixed;
  inset: 0;
  pointer-events: none;
  background:
    radial-gradient(900px 520px at 50% -10%, rgba(14, 165, 233, 0.16), transparent 65%),
    radial-gradient(760px 460px at 82% 108%, rgba(34, 197, 94, 0.1), transparent 62%);
}

.nf-number {
  font-size: 12rem;
  font-weight: 800;
  line-height: 1;
  color: rgba(255, 255, 255, 0.06);
  letter-spacing: -6px;
}

.nf-badge {
  display: flex;
  width: 96px;
  height: 96px;
  align-items: center;
  justify-content: center;
  border-radius: 24px;
  background: linear-gradient(135deg, #22c55e 0%, #0ea5e9 100%);
  box-shadow: 0 18px 40px -18px rgba(34, 197, 94, 0.7);
}

.nf-link {
  color: #7ee2a8;
  text-decoration: none;
}

.nf-link:hover {
  color: #a7f3d0;
}

@media (max-width: 520px) {
  .nf-number {
    font-size: 8rem;
  }
}
</style>
