<template>
  <div class="app-shell">
    <header class="app-header">
      <svg class="moon-icon" viewBox="0 0 64 64" xmlns="http://www.w3.org/2000/svg">
        <circle cx="32" cy="32" r="26" fill="#d4a574" />
        <circle cx="45" cy="26" r="22" fill="#0a0e27" />
      </svg>
      <h1 class="app-title">음력일정 간단등록</h1>
      <span class="version-badge">v{{ version }}</span>
      <p class="app-subtitle">2050년까지만 지원합니다</p>
    </header>
    <event-form></event-form>
  </div>
</template>

<style>
*,
*::before,
*::after {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

html, body {
  min-height: 100vh;
  background-color: #0a0e27;
  color: #e8e6e3;
  font-family: 'Noto Sans KR', 'Apple SD Gothic Neo', sans-serif;
  line-height: 1.6;
  -webkit-font-smoothing: antialiased;
}

body {
  position: relative;
  overflow-x: hidden;
}

/* Warm amber glow at bottom */
body::after {
  content: '';
  position: fixed;
  bottom: 0;
  left: 50%;
  transform: translateX(-50%);
  width: 100%;
  height: 60%;
  z-index: 0;
  pointer-events: none;
  background: radial-gradient(ellipse at 50% 100%, rgba(212, 165, 116, 0.06) 0%, transparent 70%);
}

/* Star field */
body::before {
  content: '';
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 0;
  pointer-events: none;
  background:
    radial-gradient(1.5px 1.5px at 10% 15%, rgba(255,255,255,0.7), transparent),
    radial-gradient(1px 1px at 25% 35%, rgba(255,255,255,0.5), transparent),
    radial-gradient(1.5px 1.5px at 40% 10%, rgba(255,255,255,0.6), transparent),
    radial-gradient(1px 1px at 55% 45%, rgba(255,255,255,0.4), transparent),
    radial-gradient(2px 2px at 70% 20%, rgba(212,165,116,0.5), transparent),
    radial-gradient(1px 1px at 85% 60%, rgba(255,255,255,0.5), transparent),
    radial-gradient(1.5px 1.5px at 15% 70%, rgba(255,255,255,0.4), transparent),
    radial-gradient(1px 1px at 30% 85%, rgba(255,255,255,0.6), transparent),
    radial-gradient(1px 1px at 50% 75%, rgba(255,255,255,0.3), transparent),
    radial-gradient(1.5px 1.5px at 65% 55%, rgba(255,255,255,0.5), transparent),
    radial-gradient(1px 1px at 80% 80%, rgba(255,255,255,0.4), transparent),
    radial-gradient(2px 2px at 90% 40%, rgba(212,165,116,0.4), transparent),
    radial-gradient(1px 1px at 5% 50%, rgba(255,255,255,0.5), transparent),
    radial-gradient(1.5px 1.5px at 45% 25%, rgba(255,255,255,0.3), transparent),
    radial-gradient(1px 1px at 75% 90%, rgba(255,255,255,0.5), transparent),
    radial-gradient(1px 1px at 95% 15%, rgba(255,255,255,0.4), transparent),
    radial-gradient(1.5px 1.5px at 20% 95%, rgba(255,255,255,0.3), transparent),
    radial-gradient(1px 1px at 60% 5%, rgba(255,255,255,0.6), transparent),
    radial-gradient(1px 1px at 35% 55%, rgba(123,140,173,0.5), transparent),
    radial-gradient(1.5px 1.5px at 88% 72%, rgba(255,255,255,0.4), transparent);
}
</style>

<style scoped>
.app-shell {
  position: relative;
  z-index: 1;
  max-width: 460px;
  margin: 0 auto;
  padding: 48px 32px 56px;
  margin-top: 48px;
  margin-bottom: 48px;
  background: rgba(15, 20, 50, 0.7);
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
  border-radius: 20px;
  border: 1px solid rgba(212, 165, 116, 0.25);
  box-shadow:
    0 0 40px rgba(10, 14, 39, 0.5),
    0 0 80px rgba(212, 165, 116, 0.12);
  animation: shellFadeIn 0.6s ease-out both;
}

@keyframes shellFadeIn {
  from {
    opacity: 0;
    transform: translateY(16px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.app-header {
  text-align: center;
  margin-bottom: 40px;
}

.moon-icon {
  width: 64px;
  height: 64px;
  margin-bottom: 16px;
  filter: drop-shadow(0 0 18px rgba(212, 165, 116, 0.4)) drop-shadow(0 0 40px rgba(212, 165, 116, 0.15));
}

.app-title {
  font-family: 'Cormorant Garamond', 'Georgia', serif;
  font-weight: 600;
  font-size: 1.75rem;
  color: #e8e6e3;
  letter-spacing: 0.02em;
  margin-bottom: 6px;
}

.version-badge {
  display: inline-block;
  font-size: 0.65rem;
  font-weight: 500;
  color: #0a0e27;
  background: #d4a574;
  padding: 2px 10px;
  border-radius: 12px;
  letter-spacing: 0.04em;
  vertical-align: middle;
}

.app-subtitle {
  font-size: 0.8rem;
  color: #7b8cad;
  margin-top: 8px;
}
</style>

<script>
import EventForm from './components/EventForm.vue'
import {version} from '../package.json'

export default {
  components: {
    'event-form': EventForm
  },
  data: function() {
    return {
      version: version
    }
  }
}
</script>
