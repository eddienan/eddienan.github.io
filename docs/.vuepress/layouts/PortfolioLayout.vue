<style>
/* 在这里添加样式 */
.hero {
  /* 更多自定义样式 */
}
</style>


<script setup>

import HomeHero from '../components/HomeHero.vue'
import SplineHero from '../components/SplineHero.vue'
// import HomeContent from '@theme/HomeContent.vue'
import HomeFeatures from '@theme/HomeFeatures.vue'
import HomeFooter from '../components/HomeFooter.vue'

import ShareIcon from '../components/ShareIcon.vue'

import Navbar from '@theme/Navbar.vue' 
import Sidebar from '@theme/Sidebar.vue'
import Waterfall from '../components/Waterfall.vue'

import { computed, onMounted, onUnmounted, ref } from 'vue'
import { usePageFrontmatter, useRouter } from 'vuepress/client' 
import { 
  useThemeLocaleData,
} from '@vuepress/theme-default/lib/client/composables/index.js'

import Translate from '../components/Translate.vue'
const frontmatter = usePageFrontmatter()
const themeLocale = useThemeLocaleData();

const iframe=frontmatter.value.iframe;
const splineHeroImage=frontmatter.value.splineHeroImage

// navbar
const shouldShowNavbar = computed(
  () =>
    frontmatter.value.navbar !== false && themeLocale.value.navbar !== false,
)

// sidebar
const isSidebarOpen = ref(false)
const toggleSidebar = (to )  => {
  isSidebarOpen.value = typeof to === 'boolean' ? to : !isSidebarOpen.value
}
const touchStart = { x: 0, y: 0 }
const onTouchStart = (e) => {
  touchStart.x = e.changedTouches[0].clientX
  touchStart.y = e.changedTouches[0].clientY
}
const onTouchEnd = (e)  => {
  const dx = e.changedTouches[0].clientX - touchStart.x
  const dy = e.changedTouches[0].clientY - touchStart.y
  if (Math.abs(dx) > Math.abs(dy) && Math.abs(dx) > 40) {
    if (dx > 0 && touchStart.x <= 80) {
      toggleSidebar(true)
    } else {
      toggleSidebar(false)
    }
  }
}

// classes
const containerClass = computed(() => [
  {
    'no-navbar': !shouldShowNavbar.value,
    'no-sidebar': true,
    'sidebar-open': isSidebarOpen.value,
  },
  frontmatter.value.pageClass,
])

// close sidebar after navigation
let unregisterRouterHook
onMounted(() => {

  if(iframe){
    // console.log('修改iframe样式');
    document.body.querySelector('main.home').style=`
    max-width: none;
    margin: 0px auto;
    display: block;
    margin-left: 0;
    margin-right: 0;
    padding-left: 0;
    padding-right: 0;
    `
  }

  const router = useRouter()
  unregisterRouterHook = router.afterEach(() => {
    toggleSidebar(false)
  })
})

onUnmounted(() => {
  unregisterRouterHook()
})

</script>

<template>
  <div class="theme-container" :class="containerClass" @touchstart="onTouchStart" @touchend="onTouchEnd">
    <slot name="navbar">
      <Navbar v-if="shouldShowNavbar" @toggle-sidebar="toggleSidebar">
        <template #before>
          <slot name="navbar-before" />
        </template>
        <template #after>
          <Translate />
        </template>
        <!-- todo 翻译 -->
      </Navbar>
    </slot>
    <div class="sidebar-mask" @click="toggleSidebar(false)" />
    <slot name="sidebar">
      <Sidebar>
        <template #top>
          <slot name="sidebar-top" />
        </template>
        <template #bottom>
          <slot name="sidebar-bottom" />
        </template>
      </Sidebar>
    </slot>
    <main class="home">

      <template v-if="!iframe && splineHeroImage">
        <SplineHero />
      </template>
      <template v-else-if="!iframe">
        <HomeHero />
      </template>

      <HomeFeatures />
      <!-- <header class="hero">
        <h1>ddd  VuePress 站点</h1>
        <p>这里是我的站点介绍。</p>
      </header> -->

      <Waterfall />

      <ShareIcon />

      <HomeFooter />

      <Content />

    </main>
  </div>
</template>
