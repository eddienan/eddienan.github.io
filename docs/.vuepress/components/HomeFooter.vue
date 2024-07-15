<script setup>
import { computed } from 'vue'
import { usePageFrontmatter } from 'vuepress/client'

const frontmatter = usePageFrontmatter()
const footer = computed(() => {
    if(frontmatter.value.footer&&frontmatter.value.footer.includes('{{$site.title}}')){
        frontmatter.value.footer=frontmatter.value.footer.replace('{{$site.title}}',frontmatter.value.title)
    } 
    return frontmatter.value.footer
})
const footerHtml = computed(() => {
    if(frontmatter.value.footerHtml&&frontmatter.value.footerHtml.includes('{{$site.title}}')){
        frontmatter.value.footerHtml=frontmatter.value.footerHtml.replace('{{$site.title}}',frontmatter.value.title)
    } 
    return frontmatter.value.footerHtml
})

// console.log('frontmatter.value',frontmatter.value.footer)

</script>

<template>
    <template v-if="footer">
        <!-- eslint-disable-next-line vue/no-v-html -->
        <div v-if="footerHtml" class="footer" v-html="footer" />
        <div v-else class="footer" v-text="footer" />
    </template>
</template>
