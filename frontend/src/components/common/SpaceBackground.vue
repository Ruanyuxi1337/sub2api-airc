<template>
  <canvas ref="canvasRef" class="airc-stars" aria-hidden="true"></canvas>
</template>

<script setup lang="ts">
import { onBeforeUnmount, onMounted, ref } from 'vue'

const canvasRef = ref<HTMLCanvasElement | null>(null)
let raf = 0
let cleanup: (() => void) | null = null

type Star = { x: number; y: number; r: number; a: number; sp: number; ph: number }
type Meteor = { x: number; y: number; vx: number; vy: number; life: number }

onMounted(() => {
  const canvas = canvasRef.value
  if (!canvas) return
  const ctx = canvas.getContext('2d')
  if (!ctx) return
  const dpr = Math.min(window.devicePixelRatio || 1, 2)
  let W = 0
  let H = 0
  let stars: Star[] = []
  let meteors: Meteor[] = []

  const resize = () => {
    W = canvas.width = Math.floor(window.innerWidth * dpr)
    H = canvas.height = Math.floor(window.innerHeight * dpr)
    canvas.style.width = window.innerWidth + 'px'
    canvas.style.height = window.innerHeight + 'px'
    const count = Math.round((window.innerWidth * window.innerHeight) / 5200)
    stars = []
    for (let i = 0; i < count; i++) {
      stars.push({
        x: Math.random() * W,
        y: Math.random() * H,
        r: (Math.random() * 1.8 + 0.6) * dpr,
        a: Math.random() * 0.5 + 0.5,
        sp: Math.random() * 0.0014 + 0.0007,
        ph: Math.random() * Math.PI * 2,
      })
    }
  }

  const spawnMeteor = () => {
    if (meteors.length > 1) return
    meteors.push({
      x: Math.random() * W * 0.8 + W * 0.1,
      y: Math.random() * H * 0.25,
      vx: -(5.5 + Math.random() * 3.5) * dpr,
      vy: (3.2 + Math.random() * 2.2) * dpr,
      life: 1,
    })
  }

  const frame = (t: number) => {
    ctx.clearRect(0, 0, W, H)
    for (const s of stars) {
      const tw = 0.35 + 0.65 * Math.abs(Math.sin(t * s.sp + s.ph))
      const al = s.a * tw
      if (s.r > 1.7 * dpr) {
        ctx.beginPath()
        ctx.arc(s.x, s.y, s.r * 3.2, 0, 6.2832)
        ctx.fillStyle = 'rgba(150,200,255,' + (al * 0.1).toFixed(3) + ')'
        ctx.fill()
      }
      ctx.beginPath()
      ctx.arc(s.x, s.y, s.r, 0, 6.2832)
      ctx.fillStyle = 'rgba(255,255,255,' + al.toFixed(3) + ')'
      ctx.fill()
      s.y -= (0.16 + (s.r / dpr) * 0.42) * dpr
      s.x -= (0.07 + (s.r / dpr) * 0.12) * dpr
      if (s.y < -4) {
        s.y = H + 4
        s.x = Math.random() * W
      }
      if (s.x < -4) s.x = W + 4
    }
    for (let i = meteors.length - 1; i >= 0; i--) {
      const m = meteors[i]
      m.x += m.vx
      m.y += m.vy
      m.life -= 0.01
      if (m.life <= 0 || m.y > H) {
        meteors.splice(i, 1)
        continue
      }
      const g = ctx.createLinearGradient(m.x, m.y, m.x - m.vx * 20, m.y - m.vy * 20)
      g.addColorStop(0, 'rgba(200,235,255,' + (0.85 * m.life).toFixed(2) + ')')
      g.addColorStop(1, 'rgba(200,235,255,0)')
      ctx.strokeStyle = g
      ctx.lineWidth = 1.6 * dpr
      ctx.beginPath()
      ctx.moveTo(m.x, m.y)
      ctx.lineTo(m.x - m.vx * 20, m.y - m.vy * 20)
      ctx.stroke()
    }
    if (Math.random() < 0.008) spawnMeteor()
    raf = requestAnimationFrame(frame)
  }

  resize()
  window.addEventListener('resize', resize, { passive: true })
  raf = requestAnimationFrame(frame)

  cleanup = () => {
    cancelAnimationFrame(raf)
    window.removeEventListener('resize', resize)
  }
})

onBeforeUnmount(() => cleanup?.())
</script>



<style scoped>
.airc-stars {
  position: fixed;
  inset: 0;
  width: 100%;
  height: 100%;
  z-index: 0;
  pointer-events: none;
  opacity: 0.95;
}
</style>
