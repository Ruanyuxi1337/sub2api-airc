<template>
  <AuthLayout>
    <div class="space-y-5">
      <div class="text-center">
        <h2 class="text-xl font-bold text-white">联系我们</h2>
        <p class="mt-2 text-sm text-dark-400">工作日 24 小时内回复</p>
      </div>

      <div class="c-row">
        <span>客服邮箱</span>
        <a href="mailto:support@airc.fun">support@airc.fun</a>
      </div>
      <div class="c-row">
        <span>合规举报</span>
        <a href="mailto:compliance@airc.fun">compliance@airc.fun</a>
      </div>

      <p class="text-center text-xs text-dark-500">Telegram 群组即将开放</p>
    </div>
  </AuthLayout>
</template>

<script setup lang="ts">
import { AuthLayout } from '@/components/layout'
</script>

<style scoped>
.c-row {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 12px;
  padding: 12px 14px;
  border: 1px solid rgba(255, 255, 255, 0.08);
  border-radius: 12px;
  background: rgba(3, 7, 14, 0.45);
  font-size: 13px;
}

.c-row span {
  color: #8a97ac;
}

.c-row a {
  color: #7ee2a8;
  text-decoration: none;
}

.c-row a:hover {
  color: #a7f3d0;
}
</style>
