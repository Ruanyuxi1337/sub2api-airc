<template>
  <div class="airc-auth">
    <SpaceBackground />
    <div class="airc-glow" aria-hidden="true"></div>

    <div class="airc-topbar">
      <router-link to="/" class="airc-brand">
        <img :src="logoUrl" alt="Logo" class="airc-mark" />
        <span>{{ siteName }}<small v-if="isAuthPage"> / console</small></span>
      </router-link>
      <router-link to="/model-plaza" class="airc-navlink">模型广场 →</router-link>
    </div>

    <div class="airc-wrap">
      <main class="airc-card" :class="{ 'airc-card-register': isRegister }">
        <img :src="logoUrl" alt="" class="airc-logo" />
        <template v-if="showTitle">
          <h1>{{ pageTitle }}</h1>
          <p class="airc-sub">{{ pageSubtitle }}</p>
        </template>

        <div v-if="showSeg" class="airc-seg" :class="{ reg: isRegister }">
          <div class="airc-thumb"></div>
          <router-link to="/login" :class="{ on: isLogin }">登录</router-link>
          <router-link to="/register" :class="{ on: isRegister }">注册</router-link>
        </div>

        <slot />
      </main>
    </div>

    <div class="airc-status"><span class="airc-dot"></span> 所有系统运行正常</div>
    <div class="airc-corner">{{ siteName }} · v2</div>
  </div>
</template>

<script setup lang="ts">
import { computed, onMounted } from 'vue'
import { useRoute } from 'vue-router'
import { useAppStore } from '@/stores'
import { sanitizeUrl } from '@/utils/url'
import SpaceBackground from '@/components/common/SpaceBackground.vue'

const route = useRoute()
const appStore = useAppStore()

const siteName = computed(() => appStore.siteName || 'Sub2API')
const logoUrl = computed(
  () => sanitizeUrl(appStore.siteLogo || '', { allowRelative: true, allowDataUrl: true }) || '/logo.svg',
)
const siteSubtitle = computed(
  () => appStore.cachedPublicSettings?.site_subtitle || '一个 Key，接入全部主流大模型',
)

const isLogin = computed(() => route.path === '/login')
const isRegister = computed(() => route.path === '/register')
const isAuthPage = computed(() => isLogin.value || isRegister.value)
const showSeg = computed(() => isAuthPage.value)
const showTitle = computed(() => isAuthPage.value)
const pageTitle = computed(() => (isRegister.value ? '创建你的账号' : '登录后使用服务'))
const pageSubtitle = computed(() =>
  isRegister.value ? siteSubtitle.value : '一个 Key，接入全部主流大模型',
)

onMounted(() => {
  appStore.fetchPublicSettings()
})
</script>

<style scoped>
.airc-auth {
  position: relative;
  min-height: 100vh;
  overflow: hidden;
  color: #e8eef8;
  font-family: 'Inter', 'PingFang SC', 'Hiragino Sans GB', 'Microsoft YaHei', system-ui, sans-serif;
}

.airc-glow {
  position: fixed;
  inset: 0;
  z-index: 0;
  pointer-events: none;
  animation: airc-nebula 16s ease-in-out infinite alternate;
  background:
    radial-gradient(900px 520px at 50% -10%, rgba(14, 165, 233, 0.18), transparent 65%),
    radial-gradient(760px 460px at 82% 108%, rgba(34, 197, 94, 0.12), transparent 62%),
    radial-gradient(620px 420px at 12% 96%, rgba(59, 130, 246, 0.1), transparent 65%);
}

@keyframes airc-nebula {
  from { opacity: 0.72; transform: scale(1); }
  to { opacity: 1; transform: scale(1.07); }
}

.airc-topbar {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 5;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 20px 28px;
}

.airc-brand {
  display: flex;
  align-items: center;
  gap: 10px;
  font-size: 15px;
  font-weight: 650;
  color: #e8eef8;
  text-decoration: none;
}

.airc-brand small {
  color: #7b8a9e;
  font-weight: 500;
}

.airc-mark {
  width: 28px;
  height: 28px;
  border-radius: 9px;
  box-shadow: 0 6px 18px rgba(14, 165, 233, 0.25);
}

.airc-navlink {
  padding: 8px 12px;
  border: 1px solid rgba(255, 255, 255, 0.08);
  border-radius: 9px;
  background: rgba(255, 255, 255, 0.03);
  color: #a9b7ca;
  font-size: 13px;
  text-decoration: none;
  transition: 0.18s ease;
}

.airc-navlink:hover {
  color: #fff;
  border-color: rgba(255, 255, 255, 0.16);
}

.airc-wrap {
  position: relative;
  z-index: 3;
  display: flex;
  min-height: 100vh;
  align-items: center;
  justify-content: center;
  padding: 96px 20px 80px;
}

.airc-card {
  width: 100%;
  max-width: 412px;
  padding: 34px 32px 30px;
  border: 1px solid rgba(255, 255, 255, 0.08);
  border-radius: 26px;
  background: linear-gradient(180deg, rgba(19, 28, 46, 0.86), rgba(11, 17, 30, 0.92));
  box-shadow:
    0 40px 90px -30px rgba(0, 0, 0, 0.9),
    0 24px 60px -40px rgba(34, 197, 94, 0.35);
  -webkit-backdrop-filter: blur(22px);
  backdrop-filter: blur(22px);
}

.airc-card-register {
  max-width: 452px;
}

.airc-logo {
  display: block;
  width: 58px;
  height: 58px;
  margin: 0 auto 18px;
  border-radius: 17px;
  box-shadow: 0 12px 28px -12px rgba(14, 165, 233, 0.42);
}

.airc-card h1 {
  margin: 0 0 6px;
  text-align: center;
  font-size: 23px;
  font-weight: 700;
  letter-spacing: 0.3px;
}

.airc-sub {
  margin: 0 0 22px;
  text-align: center;
  color: #aab8cb;
  font-size: 13px;
}

.airc-seg {
  position: relative;
  display: grid;
  grid-template-columns: 1fr 1fr;
  margin-bottom: 16px;
  padding: 4px;
  border: 1px solid rgba(255, 255, 255, 0.08);
  border-radius: 13px;
  background: rgba(255, 255, 255, 0.045);
}

.airc-seg a {
  position: relative;
  z-index: 2;
  display: flex;
  height: 36px;
  align-items: center;
  justify-content: center;
  border-radius: 10px;
  color: #a3b1c6;
  font-size: 13.5px;
  font-weight: 600;
  text-decoration: none;
  transition: color 0.18s ease;
}

.airc-seg a.on {
  color: #04120a;
}

.airc-thumb {
  position: absolute;
  top: 4px;
  left: 4px;
  z-index: 1;
  width: calc(50% - 4px);
  height: 36px;
  border-radius: 10px;
  background: linear-gradient(135deg, #22c55e 0%, #0ea5e9 100%);
  box-shadow: 0 8px 20px -8px rgba(34, 197, 94, 0.7);
  transition: transform 0.22s cubic-bezier(0.4, 1.4, 0.5, 1);
}

.airc-seg.reg .airc-thumb {
  transform: translateX(100%);
}

.airc-foot {
  margin-top: 20px;
  text-align: center;
  color: #93a3b8;
  font-size: 12px;
  line-height: 1.7;
}

.airc-status {
  position: fixed;
  left: 28px;
  bottom: 22px;
  z-index: 1;
  display: flex;
  align-items: center;
  gap: 8px;
  color: #6f7d92;
  font-size: 12px;
}

.airc-dot {
  width: 7px;
  height: 7px;
  border-radius: 50%;
  background: #22c55e;
  box-shadow: 0 0 0 4px rgba(34, 197, 94, 0.14);
}

.airc-corner {
  position: fixed;
  right: 28px;
  bottom: 22px;
  z-index: 1;
  color: #4c5a6d;
  font-size: 11.5px;
}

@media (max-width: 520px) {
  .airc-topbar { padding: 16px 18px; }
  .airc-wrap { align-items: flex-start; padding: 84px 16px 60px; }
  .airc-card { padding: 28px 22px 24px; border-radius: 22px; }
  .airc-brand small { display: none; }
  .airc-status,
  .airc-corner { display: none; }
}
</style>
