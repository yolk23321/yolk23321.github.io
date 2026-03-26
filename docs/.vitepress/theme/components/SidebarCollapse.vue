<script setup lang="ts">
import { ref, onMounted, watch, nextTick } from 'vue'
import { useRoute } from 'vitepress'

const route = useRoute()
const collapsed = ref(false)
const visible = ref(false)

function checkSidebar() {
  nextTick(() => {
    nextTick(() => {
      visible.value = !!document.querySelector('.VPSidebar')
    })
  })
}

function toggle() {
  collapsed.value = !collapsed.value
  document.documentElement.classList.toggle('sidebar-collapsed', collapsed.value)
}

onMounted(checkSidebar)

watch(() => route.path, () => {
  collapsed.value = false
  document.documentElement.classList.remove('sidebar-collapsed')
  checkSidebar()
})
</script>

<template>
  <Transition name="sidebar-btn">
    <button
      v-if="visible"
      class="sidebar-collapse-btn"
      :class="{ collapsed }"
      @click="toggle"
      :title="collapsed ? '展开侧边栏' : '收起侧边栏'"
    >
      <svg
        xmlns="http://www.w3.org/2000/svg"
        width="14"
        height="14"
        viewBox="0 0 24 24"
        fill="none"
        stroke="currentColor"
        stroke-width="2.5"
        stroke-linecap="round"
        stroke-linejoin="round"
      >
        <polyline points="15 18 9 12 15 6" />
      </svg>
    </button>
  </Transition>
</template>

<style>
@media (min-width: 960px) {
  .VPSidebar {
    transition: transform 0.3s ease, opacity 0.3s ease !important;
  }

  .VPContent.has-sidebar {
    transition: padding-left 0.3s ease, padding-right 0.3s ease !important;
  }

  html.sidebar-collapsed .VPSidebar {
    transform: translateX(-100%) !important;
    opacity: 0 !important;
    pointer-events: none;
  }

  html.sidebar-collapsed .VPContent.has-sidebar {
    padding-left: 0 !important;
  }
}

@media (min-width: 960px) {
  html.sidebar-collapsed .VPDoc.has-sidebar .container {
    display: flex;
    justify-content: center;
    max-width: 992px;
  }
}

@media (min-width: 1440px) {
  html.sidebar-collapsed .VPContent.has-sidebar {
    padding-right: 0 !important;
    padding-left: 0 !important;
  }

  html.sidebar-collapsed .VPDoc.has-sidebar .container {
    max-width: 1104px;
  }
}
</style>

<style scoped>
.sidebar-collapse-btn {
  display: none;
}

@media (min-width: 960px) {
  .sidebar-collapse-btn {
    display: flex;
    align-items: center;
    justify-content: center;
    position: fixed;
    top: 50%;
    left: var(--vp-sidebar-width);
    transform: translateX(-50%) translateY(-50%);
    z-index: calc(var(--vp-z-index-sidebar, 20) + 1);
    width: 22px;
    height: 52px;
    border-radius: 6px;
    border: 1px solid var(--vp-c-divider);
    background: var(--vp-c-bg);
    color: var(--vp-c-text-3);
    cursor: pointer;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.06);
    transition: left 0.3s ease, color 0.2s, border-color 0.2s,
      box-shadow 0.2s, opacity 0.2s, border-radius 0.3s ease;
    opacity: 0.5;
  }

  .sidebar-collapse-btn:hover {
    color: var(--vp-c-brand-1);
    border-color: var(--vp-c-brand-1);
    box-shadow: 0 2px 12px rgba(0, 0, 0, 0.1);
    opacity: 1;
  }

  .sidebar-collapse-btn svg {
    transition: transform 0.3s ease;
  }

  /* 收起后贴齐视口左缘，竖条长方形 */
  .sidebar-collapse-btn.collapsed {
    left: 0;
    transform: translateY(-50%);
    border-left: none;
    border-radius: 0 6px 6px 0;
    opacity: 0.85;
  }

  .sidebar-collapse-btn.collapsed:hover {
    opacity: 1;
  }

  .sidebar-collapse-btn.collapsed svg {
    transform: rotate(180deg);
  }
}

@media (min-width: 1440px) {
  .sidebar-collapse-btn:not(.collapsed) {
    left: calc(
      (100vw - (var(--vp-layout-max-width) - 64px)) / 2
      + var(--vp-sidebar-width) - 32px
    );
  }
}

.sidebar-btn-enter-active,
.sidebar-btn-leave-active {
  transition: opacity 0.3s ease;
}

.sidebar-btn-enter-from,
.sidebar-btn-leave-to {
  opacity: 0 !important;
}
</style>
