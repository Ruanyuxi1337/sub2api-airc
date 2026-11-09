<template>
  <!-- Admin-provided full-page home content keeps priority -->
  <div v-if="hasHomeContent" class="min-h-screen">
    <iframe
      v-if="isHomeContentUrl"
      :src="homeContent.trim()"
      class="h-screen w-full border-0"
      allowfullscreen
    ></iframe>
    <div v-else v-html="homeContent"></div>
  </div>

  <div v-else class="ah-root">
    <SpaceBackground />
    <div class="ah-glow" aria-hidden="true"></div>

    <nav class="ah-nav">
      <div class="ah-nav-inner">
        <router-link to="/" class="ah-brand">
          <img :src="logoUrl" alt="Logo" class="ah-mark" />
          {{ siteName }}
        </router-link>
        <div class="ah-links">
          <router-link v-if="showModelPlazaEntry" to="/model-plaza">模型广场</router-link>
          <router-link :to="isAuthenticated ? dashboardPath : '/login'">控制台</router-link>
        </div>
        <div class="ah-right">
          <template v-if="isAuthenticated">
            <router-link class="ah-ghost" :to="dashboardPath">进入控制台</router-link>
          </template>
          <template v-else>
            <router-link class="ah-ghost" to="/login">登录</router-link>
            <router-link class="ah-solid" to="/login?tab=register">免费注册</router-link>
          </template>
        </div>
      </div>
    </nav>

    <div class="ah-wrap">
      <header class="ah-hero">
        <span class="ah-badge">● {{ stats.models || '—' }} 个模型在线 · 按官方价 {{ minRateText }} 起</span>
        <h1>一个 Key，<span class="ah-grad">接入所有主流模型</span></h1>
        <p class="ah-lead">
          OpenAI 兼容接口，改一行 base_url 就能用。按量计费、余额不过期，<br />
          每个模型都标注真实上游与官方价对比。
        </p>
        <div class="ah-ctas">
          <router-link class="ah-solid ah-big" to="/login?tab=register">免费注册</router-link>
          <router-link class="ah-ghost ah-big" to="/model-plaza">查看模型广场 →</router-link>
        </div>

        <div class="ah-term">
          <div class="ah-term-bar"><i></i><i></i><i></i></div>
<pre><span class="c"># Claude Code / Cursor</span>
<span class="k">export</span> ANTHROPIC_BASE_URL=<span class="s">"{{ apiBaseUrl }}"</span>
<span class="k">export</span> ANTHROPIC_AUTH_TOKEN=<span class="s">"sk-..."</span>

<span class="c"># 任何 OpenAI SDK / 脚本</span>
<span class="k">export</span> OPENAI_BASE_URL=<span class="s">"{{ apiBaseUrl }}/v1"</span>
<span class="k">export</span> OPENAI_API_KEY=<span class="s">"sk-..."</span>

curl {{ apiBaseUrl }}/v1/chat/completions \
  -H <span class="s">"Authorization: Bearer ***"</span> \
  -d <span class="s">'{"model":"claude-sonnet-5-5","messages":[{"role":"user","content":"hi"}]}'</span></pre>
        </div>

        <div class="ah-stats">
          <div class="ah-stat"><b>{{ stats.models || '—' }}</b><span>在线模型</span></div>
          <div class="ah-stat"><b>{{ stats.groups || '—' }}</b><span>可用线路</span></div>
          <div class="ah-stat"><b>{{ minRateText }}</b><span>官方价折扣</span></div>
          <div class="ah-stat"><b>{{ rechargeText }}</b><span>充值汇率</span></div>
        </div>
      </header>

      <section id="pricing" class="ah-section">
        <h2>按线路定价，价格公开</h2>
        <p class="ah-sub">实付价 = 官方价 × 分组倍率，充值时按 {{ rechargeText }} 入账。</p>
        <div class="ah-grid4">
          <div v-for="card in priceCards" :key="card.title" class="ah-price">
            <h3>{{ card.title }}</h3>
            <div class="ah-tag">{{ card.tag }}</div>
            <div class="ah-rate">{{ card.rate }}<small>官方价</small></div>
            <ul>
              <li v-for="item in card.items" :key="item">{{ item }}</li>
            </ul>
          </div>
        </div>
      </section>

      <section class="ah-section">
        <h2>为什么选我们</h2>
        <p class="ah-sub">不靠形容词，靠可核对的东西。</p>
        <div class="ah-feats">
          <div class="ah-feat">
            <h4>上游透明</h4>
            <p>每个模型标注真实上游类型与官方价，不写“官转”这类模糊说法。</p>
          </div>
          <div class="ah-feat">
            <h4>多账号自动故障转移</h4>
            <p>单账号限流或掉线自动切下一个，客户端无感。</p>
          </div>
          <div class="ah-feat">
            <h4>余额不过期 · 按量计费</h4>
            <p>没有套餐绑定，没有月费，用多少扣多少。</p>
          </div>
        </div>
      </section>

      <section id="faq" class="ah-section">
        <h2>常见问题</h2>
        <p class="ah-sub">还有疑问，发邮件到 support@airc.fun，工作日 24 小时内回复。</p>
        <div class="ah-feats">
          <div class="ah-feat">
            <h4>注册有免费额度吗？</h4>
            <p>注册不赠送额度。充值后按 {{ rechargeText }} 入账，充 100 元到账 16.5 美元；邀请好友充值，邀请人再返 10%。</p>
          </div>
          <div class="ah-feat">
            <h4>充值和计费怎么算？</h4>
            <p>1 元 = 0.15 美元额度（约 6.67 元 = 1 美元）。充值赠送 10%，收银台另收 1.5% 通道手续费。</p>
          </div>
          <div class="ah-feat">
            <h4>接口兼容吗？</h4>
            <p>OpenAI 兼容 <code>/v1/chat/completions</code>、<code>/v1/models</code>；Claude 原生 <code>/v1/messages</code>。</p>
          </div>
          <div class="ah-feat">
            <h4>能开发票 / 退款吗？</h4>
            <p>可以。发送订单号到 support@airc.fun，发票与退款按原支付渠道处理。</p>
          </div>
        </div>
      </section>

      <footer class="ah-footer">
        <div>© {{ currentYear }} {{ siteName }} · support@airc.fun</div>
        <div class="ah-flinks">
          <a href="/user-agreement">服务条款</a>
          <a href="/privacy-policy">隐私政策</a>
        </div>
      </footer>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, onMounted, ref } from 'vue'
import { useAuthStore, useAppStore } from '@/stores'
import { sanitizeUrl } from '@/utils/url'
import SpaceBackground from '@/components/common/SpaceBackground.vue'

type PlazaModel = { name?: string }
type PlazaGroup = {
  name?: string
  platform?: string
  rate_multiplier?: number | string
  models?: PlazaModel[]
}
type PriceCard = { title: string; tag: string; rate: string; items: string[] }

const authStore = useAuthStore()
const appStore = useAppStore()

const siteName = computed(() => appStore.cachedPublicSettings?.site_name || appStore.siteName || 'Airc API')
const logoUrl = computed(
  () => sanitizeUrl(appStore.cachedPublicSettings?.site_logo || appStore.siteLogo || '', {
    allowRelative: true,
    allowDataUrl: true,
  }) || '/logo.svg',
)
const homeContent = computed(() => appStore.cachedPublicSettings?.home_content || '')
const hasHomeContent = computed(() => homeContent.value.trim().length > 0)
const isHomeContentUrl = computed(() => {
  const c = homeContent.value.trim()
  return c.startsWith('http://') || c.startsWith('https://')
})
const apiBaseUrl = computed(() => (appStore.cachedPublicSettings?.api_base_url || 'https://api.airc.fun').replace(/\/+$/, ''))

const isAuthenticated = computed(() => authStore.isAuthenticated)
const isAdmin = computed(() => authStore.isAdmin)
const dashboardPath = computed(() => (isAdmin.value ? '/admin/dashboard' : '/dashboard'))
const modelPlazaRequiresAuth = computed(
  () => appStore.cachedPublicSettings?.model_plaza_require_auth === true,
)
const modelPlazaEnabled = computed(
  () => appStore.cachedPublicSettings?.model_plaza_enabled !== false,
)
const showModelPlazaEntry = computed(
  () => modelPlazaEnabled.value && (modelPlazaRequiresAuth.value === false || isAuthenticated.value),
)
const settingsRecord = computed(
  () => (appStore.cachedPublicSettings ?? {}) as unknown as Record<string, unknown>,
)

const currentYear = computed(() => new Date().getFullYear())

const stats = ref<{ models: number; groups: number; minRate: number | null }>({
  models: 0,
  groups: 0,
  minRate: null,
})

const priceCards = ref<PriceCard[]>([
  { title: 'Claude 专线', tag: 'Anthropic 官方口径', rate: '—', items: ['Sonnet / Opus 5.5 全系', '1M 上下文', '支持 prompt caching'] },
  { title: '企业级 GPT', tag: 'OpenAI 兼容', rate: '—', items: ['GPT-6 Astra / 6 Sol / 6.1 Sol', '922K–1.05M 上下文', '工具调用 / 流式'] },
  { title: 'Gemini 极速', tag: 'Flash 全系', rate: '—', items: ['3.8 / 3.5 Flash · Argon 4', '图片生成', '超长上下文'] },
  { title: '国产大模型', tag: 'Kimi / 智谱 / DeepSeek', rate: '—', items: ['Kimi K3 / K3.1 Pro', 'DeepSeek V4 / V4.1 Flash', 'GLM-5.3 / GLM-5.2'] },
])

const rechargeText = computed(() => {
  const m = Number(settingsRecord.value.payment_balance_recharge_multiplier)
  if (!Number.isFinite(m) || m <= 0) return '1 元 = 0.15 美元'
  return `1 元 = ${m.toFixed(2).replace(/0+$/, '').replace(/\.$/, '')} 美元`
})

const minRateText = computed(() => (stats.value.minRate === null ? '—' : formatRate(stats.value.minRate)))

function formatRate(rate: number): string {
  if (!Number.isFinite(rate)) return '—'
  const floored = Math.floor(rate * 100) / 100
  return `${floored.toFixed(2).replace(/0+$/, '').replace(/\.$/, '')}×`
}

function familyMin(groups: PlazaGroup[], match: (g: PlazaGroup) => boolean): number | null {
  let min: number | null = null
  for (const g of groups) {
    if (!match(g)) continue
    const r = Number(g.rate_multiplier)
    if (Number.isFinite(r) && (min === null || r < min)) min = r
  }
  return min
}

async function loadPlaza() {
  try {
    const res = await fetch('/api/v1/model-plaza')
    if (!res.ok) return
    const payload = await res.json()
    const data = payload?.data ?? payload
    const groups: PlazaGroup[] = Array.isArray(data?.groups) ? data.groups : []
    const names = new Set<string>()
    let min: number | null = null
    for (const g of groups) {
      const r = Number(g.rate_multiplier)
      if (Number.isFinite(r) && (min === null || r < min)) min = r
      for (const m of g.models ?? []) {
        if (m?.name) names.add(String(m.name))
      }
    }
    stats.value = { models: names.size, groups: groups.length, minRate: min }

    const cpu = familyMin(groups, (g) => g.platform === 'kimi' || g.platform === 'zhipu' || g.platform === 'deepseek')
    const claude = familyMin(groups, (g) => g.platform === 'anthropic' || /claude|ccmax/i.test(g.name || ''))
    const gpt = familyMin(groups, (g) => g.platform === 'openai')
    const gemini = familyMin(groups, (g) => g.platform === 'antigravity' && /gemini/i.test(g.name || ''))
    const rates = [claude, gpt, gemini, cpu]
    priceCards.value = priceCards.value.map((card, i) => ({
      ...card,
      rate: rates[i] === null ? '—' : formatRate(rates[i] as number),
    }))
  } catch {
    // keep fallback values
  }
}

onMounted(() => {
  authStore.checkAuth()
  appStore.fetchPublicSettings()
  void loadPlaza()
})
</script>

<style scoped>
.ah-root {
  position: relative;
  min-height: 100vh;
  overflow-x: hidden;
  color: #e8eef8;
  font-family: 'Inter', 'PingFang SC', 'Hiragino Sans GB', 'Microsoft YaHei', system-ui, sans-serif;
  -webkit-font-smoothing: antialiased;
}

.ah-glow {
  position: fixed;
  inset: 0;
  z-index: 0;
  pointer-events: none;
  animation: ah-nebula 16s ease-in-out infinite alternate;
  background:
    radial-gradient(1000px 560px at 50% -12%, rgba(14, 165, 233, 0.2), transparent 62%),
    radial-gradient(800px 460px at 88% 112%, rgba(34, 197, 94, 0.12), transparent 62%);
}

@keyframes ah-nebula {
  from { opacity: 0.72; transform: scale(1); }
  to { opacity: 1; transform: scale(1.07); }
}

.ah-wrap {
  position: relative;
  z-index: 2;
  max-width: 1120px;
  margin: 0 auto;
  padding: 0 24px;
}

.ah-nav {
  position: sticky;
  top: 0;
  z-index: 10;
  border-bottom: 1px solid rgba(255, 255, 255, 0.05);
  background: rgba(5, 7, 13, 0.6);
  -webkit-backdrop-filter: blur(18px);
  backdrop-filter: blur(18px);
}

.ah-nav-inner {
  display: flex;
  max-width: 1120px;
  margin: 0 auto;
  align-items: center;
  gap: 28px;
  padding: 14px 24px;
}

.ah-brand {
  display: flex;
  align-items: center;
  gap: 10px;
  color: inherit;
  font-size: 15px;
  font-weight: 650;
  text-decoration: none;
}

.ah-mark {
  width: 28px;
  height: 28px;
  border-radius: 9px;
}

.ah-links {
  display: flex;
  gap: 24px;
  margin-left: 8px;
  font-size: 13.5px;
  color: #8a97ac;
}

.ah-links a {
  color: inherit;
  text-decoration: none;
}

.ah-links a:hover {
  color: #fff;
}

.ah-right {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-left: auto;
}

.ah-ghost,
.ah-solid {
  display: inline-flex;
  height: 36px;
  align-items: center;
  justify-content: center;
  box-sizing: border-box;
  padding: 0 18px;
  border-radius: 10px;
  font-size: 13px;
  line-height: 1;
  text-decoration: none;
  white-space: nowrap;
}

.ah-ghost {
  border: 1px solid rgba(255, 255, 255, 0.08);
  background: transparent;
  color: #cfdaea;
}

.ah-ghost:hover {
  border-color: rgba(255, 255, 255, 0.16);
  color: #fff;
}

.ah-solid {
  border: 0;
  background: linear-gradient(135deg, #22c55e 0%, #0ea5e9 100%);
  color: #04120a;
  font-weight: 700;
  box-shadow: 0 12px 28px -12px rgba(34, 197, 94, 0.7);
}

.ah-hero {
  padding: 76px 0 40px;
  text-align: center;
}

.ah-badge {
  display: inline-flex;
  height: 30px;
  align-items: center;
  gap: 8px;
  padding: 0 13px;
  border: 1px solid rgba(14, 165, 233, 0.32);
  border-radius: 999px;
  background: rgba(14, 165, 233, 0.1);
  color: #9fd4f2;
  font-size: 12px;
}

.ah-hero h1 {
  margin: 20px 0 14px;
  font-size: 52px;
  font-weight: 800;
  letter-spacing: -0.5px;
  line-height: 1.14;
}

.ah-grad {
  background: linear-gradient(135deg, #22c55e 0%, #0ea5e9 100%);
  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;
}

.ah-lead {
  max-width: 620px;
  margin: 0 auto;
  color: #8a97ac;
  font-size: 15.5px;
  line-height: 1.75;
}

.ah-ctas {
  display: flex;
  justify-content: center;
  gap: 12px;
  margin-top: 30px;
}

.ah-big {
  height: 46px;
  padding: 0 26px;
  border-radius: 12px;
  font-size: 14.5px;
}

.ah-term {
  max-width: 720px;
  margin: 34px auto 0;
  overflow: hidden;
  border: 1px solid rgba(255, 255, 255, 0.08);
  border-radius: 16px;
  background: linear-gradient(180deg, rgba(17, 26, 44, 0.9), rgba(9, 14, 24, 0.95));
  box-shadow: 0 40px 80px -40px rgba(0, 0, 0, 0.9);
}

.ah-term-bar {
  display: flex;
  align-items: center;
  gap: 7px;
  padding: 11px 14px;
  border-bottom: 1px solid rgba(255, 255, 255, 0.06);
}

.ah-term-bar i {
  display: block;
  width: 10px;
  height: 10px;
  border-radius: 50%;
  background: #2b3648;
}

.ah-term pre {
  margin: 0;
  padding: 18px;
  overflow: auto;
  color: #9fb3cc;
  font-family: 'JetBrains Mono', 'SFMono-Regular', Consolas, monospace;
  font-size: 12.6px;
  line-height: 1.85;
  text-align: left;
}

.ah-term .k { color: #7dd3fc; }
.ah-term .s { color: #86efac; }
.ah-term .c { color: #4b5a70; }

.ah-stats {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 14px;
  margin-top: 56px;
}

.ah-stat {
  padding: 18px 20px;
  border: 1px solid rgba(255, 255, 255, 0.08);
  border-radius: 14px;
  background: rgba(255, 255, 255, 0.022);
}

.ah-stat b {
  display: block;
  font-size: 22px;
  letter-spacing: -0.2px;
}

.ah-stat span {
  color: #5d6a7e;
  font-size: 12.5px;
}

.ah-section {
  padding: 64px 0 0;
}

.ah-section h2 {
  margin: 0 0 8px;
  font-size: 26px;
  font-weight: 700;
  letter-spacing: -0.2px;
}

.ah-sub {
  margin: 0 0 26px;
  color: #8a97ac;
  font-size: 14px;
}

.ah-grid4 {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 14px;
}

.ah-price {
  padding: 20px;
  border: 1px solid rgba(255, 255, 255, 0.08);
  border-radius: 16px;
  background: linear-gradient(180deg, rgba(255, 255, 255, 0.03), rgba(255, 255, 255, 0.01));
}

.ah-price h3 {
  margin: 0 0 4px;
  font-size: 15px;
}

.ah-tag {
  color: #5d6a7e;
  font-size: 11.5px;
}

.ah-rate {
  margin: 14px 0 4px;
  font-size: 28px;
  font-weight: 800;
  letter-spacing: -0.5px;
}

.ah-rate small {
  margin-left: 4px;
  color: #5d6a7e;
  font-size: 12px;
  font-weight: 500;
}

.ah-price ul {
  margin: 14px 0 0;
  padding: 0;
  color: #8a97ac;
  font-size: 12.5px;
  line-height: 2;
  list-style: none;
}

.ah-price li::before {
  content: '·';
  margin-right: 8px;
  color: #5d6a7e;
}

.ah-feats {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 14px;
}

.ah-feat {
  padding: 22px;
  border: 1px solid rgba(255, 255, 255, 0.08);
  border-radius: 16px;
  background: rgba(255, 255, 255, 0.022);
}

.ah-feat h4 {
  margin: 0 0 6px;
  font-size: 14.5px;
}

.ah-feat p {
  margin: 0;
  color: #8a97ac;
  font-size: 13px;
  line-height: 1.7;
}

.ah-feat code {
  padding: 1px 5px;
  border-radius: 5px;
  background: rgba(255, 255, 255, 0.06);
  font-size: 12px;
}

.ah-footer {
  display: flex;
  justify-content: space-between;
  margin-top: 70px;
  padding: 26px 0 40px;
  border-top: 1px solid rgba(255, 255, 255, 0.06);
  color: #5d6a7e;
  font-size: 12.5px;
}

.ah-flinks {
  display: flex;
  gap: 18px;
}

.ah-flinks a {
  color: inherit;
  text-decoration: none;
}

.ah-flinks a:hover {
  color: #b9c8da;
}

@media (max-width: 900px) {
  .ah-hero h1 { font-size: 36px; }
  .ah-stats { grid-template-columns: repeat(2, 1fr); }
  .ah-grid4 { grid-template-columns: repeat(2, 1fr); }
  .ah-feats { grid-template-columns: 1fr; }
  .ah-links { display: none; }
}

@media (max-width: 520px) {
  .ah-hero h1 { font-size: 30px; }
  .ah-ctas { flex-direction: column; }
  .ah-grid4 { grid-template-columns: 1fr; }
  .ah-hero { padding: 52px 0 24px; }
}
</style>
