<script setup>
import { ref, onMounted } from 'vue'
import { usePageFrontmatter } from 'vuepress/client'

const frontmatter = usePageFrontmatter()
const waterfallData = ref([]);
const waterfallTitle=ref(frontmatter.value.waterfallTitle||'');

//let _waterfallCount=frontmatter.value.waterfall.length;
let _waterfallIndex=12;

const screenWidth=ref(0);

function scaleElement(width, height, maxHeight) {
  if (height > maxHeight) {
    const ratio = maxHeight / height;
    height = maxHeight;
    width *= ratio;
  }
  return { width, height };
}

function loadImage(imageUrl) {
  return new Promise((resolve, reject) => {
    const image = new Image()
    image.src = imageUrl

    image.onload = function () {
      const scaledSize = scaleElement(image.width/2, image.height/2, 450);
      resolve({ ...scaledSize, url: imageUrl })
    }

    image.onerror = function () {
      reject(new Error('Failed to load image'))
    }
  })
}

// async function loadImages(urls) {
//   const promises = urls.map(url => loadImage(url))
//   try {
//     const images = await Promise.all(promises)
//     return images
//   } catch (error) {
//     console.error(error)
//     return []
//   }
// }

async function loadImages(urls, callback) {
  const promises = urls.map(url => loadImage(url))
  const results = await Promise.allSettled(promises)
  results.forEach((result,index) => {
    if (result.status === 'fulfilled') {
        callback({...result.value,index})
    //   setTimeout(()=>callback({...result.value,index}),Math.random()*5000)
    } else {
      console.error(result.reason)
    }
  })
}

//提取url的域名
function extractDomain(post) {
    // 正则表达式匹配URL
    const urlPattern = /http[s]?:\/\/[^\s]+/;
    const urlMatch = post.match(urlPattern);
    
    if (!urlMatch) {
        return null;
    }

    try {
        const url = urlMatch[0];
    const urlObj = new URL(url);
    return urlObj.hostname;
    } catch (error) {
        return null
    }
   
}


//随机排序
function shuffleArray(array) {
    for (let i = array.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [array[i], array[j]] = [array[j], array[i]];
    }
    return array;
}

const _initWaterFall = () => setTimeout(() => {
    if (waterfall&&document.querySelector('.waterfall')) {
      waterfall('.waterfall')
    } else {
      _initWaterFall()
    }
  }, 600)

onMounted(async () => {

  screenWidth.value = window.screen.width;
    if(frontmatter.value.waterfall){
        let data = [...frontmatter.value.waterfall].slice(0,_waterfallIndex);
        data=shuffleArray(data)
        // console.log('screenWidth',screenWidth)
        await loadImages(data.map(d => d.image),(r)=>{
            data[r.index].width = r.width
            data[r.index].height = r.height;

            if(data[r.index].posts){
                data[r.index].domain=data[r.index].domain||extractDomain(data[r.index].posts)
            }
            
            waterfallData.value = [...data]
            _initWaterFall();
        })

        _initWaterFall();
    }
 

  window.addEventListener('resize', function () {
    _initWaterFall()
    });


 const loadMore=async()=>{
    let scrollHeight = document.documentElement.scrollHeight;
        let scrollTop = document.documentElement.scrollTop || document.body.scrollTop;
        let clientHeight = document.documentElement.clientHeight;
        console.log(scrollTop + clientHeight , scrollHeight)
        if (scrollTop + clientHeight >= scrollHeight) { 
            document.querySelector('.waterfall .loading').style.display = 'block';

            window._waterfallIndex+=6;
            if(window._waterfallIndex<=window._waterfallCount){
                let data = [...frontmatter.value.waterfall].slice(0,window._waterfallIndex);
                data=shuffleArray(data)
                await loadImages(data.map(d => d.image),(r)=>{
                    data[r.index].width = r.width
                    data[r.index].height = r.height;
                    if(data[r.index].posts){
                        data[r.index].domain=data[r.index].domain||extractDomain(data[r.index].posts)
                    }
                    waterfallData.value = [...data]
                    _initWaterFall();
                })

                document.querySelector('.waterfall .loading').style.display = 'none';
 
            }else{
                document.querySelector('.waterfall .loading').style.display = 'none';
            }
            

        }
 }

    window.addEventListener('scroll',async()=> {
        if(document.querySelector('.waterfall .loading')){
            loadMore()
        }
    });

    document.addEventListener('wheel', async (event)=> {
        if (event.deltaY > 0) {
            if(document.querySelector('.waterfall .loading')){
            loadMore()
        } 
        } 
    });

})


</script>

<style>
@keyframes anim {
    0% {
        opacity: 0;
    }

    100% {
        opacity: 1;
    }
}

.waterfall .header {
    padding-block: min(20vh, 2rem);
    width: calc(min(76.5rem, 90%));
    margin-inline: auto;
    color: var(--c-text);

    h2 {
        text-transform: capitalize;
        letter-spacing: 0.025em;
        font-size: clamp(2rem, 1.8125rem + 0.75vw, 2.6rem);
    }
}

.waterfall .item {
    max-height: 560px;
    background: #000000e0 center center no-repeat;
    background-size: contain;
    text-align: center;
    display: inline-block;
    font-size: 40px;
    color: #FFF;
    width: 220px !important;
    margin: 8px;
    animation: anim 2s ease-in-out;
    cursor: pointer;
    border-radius: 24px;
}

.waterfall .item .title {
    font-size: 14px;
    font-weight: 800;
}

.waterfall .item .details {
    font-size: 12px;
    padding: 0 12px;
}

.waterfall .item .text {
    height: 100%;
    background: linear-gradient(to top, rgba(0, 0, 0, 0.805), rgba(255, 255, 255, 0));
    display: flex;
    justify-content: flex-end;
    align-items: center;
    flex-direction: column;
    border-radius: 24px;
}

.waterfall .domain {
    line-height: 13px;
    font-size: 12px;
    margin: 14px;
    padding: 3px 5px;
    background: var(--c-brand);
    width: fit-content;
    border-radius: 24px;
    position: absolute;
}

.waterfall .a:hover {
    filter: brightness(0.5);
    background: #000000b8;
    content: "ffff";
}

@keyframes spin {
    0% {
        transform: rotate(0deg);
    }

    100% {
        transform: rotate(360deg);
    }
}

.waterfall .loading {
    width: 50px;
    height: 50px;
    border: 5px solid #f3f3f3;
    border-top: 5px solid #3498db;
    border-radius: 50%;
    animation: spin 1s linear infinite;
    display: none;
}


.vp-back-to-top-button {
    --back-to-top-color: #464a5f;
    --back-to-top-color-hover: #9E9E9E;
    --back-to-top-bg-color: var(--c-bg);
}
</style>

<template>
    <div class="header" v-if="waterfallTitle">
        <h2 v-html="waterfallTitle"> </h2>
    </div>

    <div v-if="waterfallData.length" class="waterfall">
        <div v-for="item in waterfallData" :key="item.title">
            <template v-if="item.posts">
                <a class="item" :href="'posts/' + item.posts + '.html'" v-if="item.width" :style="{
        'background-image': 'url(' + encodeURI(item.image) + ')',
        width: screenWidth < 500 ? 'calc(' + screenWidth + 'px - 5rem)!important' : 'auto',
        height: item.height + 'px'
    }">

                    <p class="domain" v-if="item.domain">{{ item.domain }}</p>
                    <div class="text a" :style="{ height: item.height + 'px' }">
                        <p class="title">{{ item.title }}</p>
                        <p class="details">{{ item.details }}</p>
                    </div>
                </a>
            </template>
            <template v-else>
                <div class="item" style="cursor: inherit;" v-if="item.width" :style="{
        'background-image': 'url(' + encodeURI(item.image) + ')',
        width: screenWidth < 500 ? 'calc(' + screenWidth + 'px - 5rem)!important' : 'auto',
        height: item.height + 'px'
    }">
                    <div class="text" :style="{ height: item.height + 'px' }">
                        <p class="title">{{ item.title }}</p>
                        <p class="details">{{ item.details }}</p>
                    </div>
                </div>
            </template>
        </div>
        <div class="loading"></div>
    </div>
</template>