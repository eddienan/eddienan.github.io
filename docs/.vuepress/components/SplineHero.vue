<script setup>
import { computed, h,nextTick } from 'vue'
import {
  AutoLink,
  ClientOnly,
  usePageFrontmatter,
  useSiteLocaleData,
  withBase,
} from 'vuepress/client'

import { useDarkMode } from '@vuepress/theme-default/lib/client/composables/index.js'

const frontmatter = usePageFrontmatter()
const siteLocale = useSiteLocaleData()
const isDarkMode = useDarkMode()

const heroImage = computed(() => {
  // if (isDarkMode.value && frontmatter.value.heroImageDark !== undefined) {
  //   return frontmatter.value.heroImageDark
  // }
  return frontmatter.value.splineHeroImage
})

if(heroImage&&typeof document !== 'undefined'){
  const script = document.createElement('script');
    script.src = "https://unpkg.com/@splinetool/viewer@1.4.7/build/spline-viewer.js"; // 替换为你的外部 URL
    script.async = true;
    script.type='module'
    script.onload = () => {
      // 你可以在这里执行任何需要的操作
        const splineViewers = document.querySelectorAll('main spline-viewer');
        for (const splineViewer of splineViewers) {
          // console.log('Script loaded successfully',splineViewer.shadowRoot);
        
          if (splineViewer && splineViewer.shadowRoot) {
            const style = document.createElement('style');
            style.innerHTML = `
              #logo {
                display: none !important;
              }
            `;
            splineViewer.shadowRoot.appendChild(style);
          }
        }
        
    };
    script.onerror = () => {
      console.error('Failed to load the script');
    };
    document.head.appendChild(script);
 
}

const heroAlt = computed(
  () => frontmatter.value.heroAlt || heroText.value || 'hero',
)
const heroHeight = computed(() => frontmatter.value.heroHeight || 280)

const heroText = computed(() => {
  if (frontmatter.value.heroText === null) {
    return null
  }
  return frontmatter.value.heroText || siteLocale.value.title || 'Hello'
})

const tagline = computed(() => {
  if (frontmatter.value.tagline === null) {
    return null
  }
  return (
    frontmatter.value.tagline ||
    siteLocale.value.description ||
    'Welcome to your VuePress site'
  )
})

const actions = computed(() => {
  if (!Array.isArray(frontmatter.value.actions)) {
    return []
  }

  return frontmatter.value.actions.map(({ text, link, type = 'primary' }) => ({
    text,
    link,
    type,
  }))
})

const HomeHeroImage = () => {
  if (!heroImage.value) return null
  const img = h('spline-viewer', {
    url: withBase(heroImage.value),
    alt: heroAlt.value,
    height: heroHeight.value,
  });

  // console.log('img',img)
  if (img && img.shadowRoot) {
      const style = document.createElement('style');
      style.innerHTML = `
        #logo {
          display: none !important;
        }
      `;
      img.shadowRoot.appendChild(style);
    }

  if (frontmatter.value.heroImageDark === undefined) {
    return img
  }
  // wrap hero image with <ClientOnly> to avoid ssr-mismatch
  // when using a different hero image in dark mode
  return h(ClientOnly, () => img)
}
</script>

<style>
.home .hero .actions .action-button.primary {
  color: #ffffff;
  background-color: #2f2f2f;
  border-color: #cecece;
}

.home .hero .actions .action-button.secondary {
  color: #414141;
  background-color: #ffffff;
  border-color: #222222;
}

.home .hero .actions .action-button.secondary:hover {
  color: #ffffff;
  background-color: #2f2f2f;
  border-color: #cecece;
}


.home .hero .dark_mode .action-button.primary {
  color: #414141;
  background-color: #ffffff;
  border-color: #222222;
}

.home .hero .dark_mode .action-button.secondary {
  color: #ffffff;
  background-color: #2f2f2f;
  border-color: #cecece;

}

.home .hero .dark_mode .action-button.primary:hover {
  color: #ffffff;
  background-color: #2f2f2f;
  border-color: #cecece;
}
</style>

<template>
  <header class="hero">
    <HomeHeroImage />
    <h1 v-if="heroText" id="main-title">
      {{ heroText }}
    </h1>

    <p v-if="tagline" class="description">
      {{ tagline }}
    </p>

    <p v-if="actions.length" class="actions" :class="{ dark_mode: isDarkMode }">
      <AutoLink v-for="action in actions" :key="action.text" class="action-button" :class="[action.type]"
        :config="action" />
    </p>
  </header>
</template>
