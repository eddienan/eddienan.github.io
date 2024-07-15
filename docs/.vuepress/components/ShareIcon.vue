<script setup>
import { ref, onMounted } from 'vue'
import { usePageFrontmatter } from 'vuepress/client'
import QrcodeVue from 'qrcode.vue'
// import 'font-awesome/css/font-awesome.min.css'
// import 'font-awesome/fonts/fontawesome-webfont.svg'
const frontmatter = usePageFrontmatter()

//用来添加翻译参数
function addURLParameter(key, value) {
  var url = window.location.href;
  var updatedURL = '';
  
  if (url.indexOf('?') !== -1) {
    var params = url.split('?')[1].split('&');
    var paramFound = false;
    
    for (var i = 0; i < params.length; i++) {
      var param = params[i].split('=');
      
      if (param[0] === key) {
        param[1] = encodeURIComponent(value);
        paramFound = true;
      }
      
      params[i] = param.join('=');
    }
    
    updatedURL = url.split('?')[0] + '?' + params.join('&');
    
    if (!paramFound) {
      updatedURL += '&' + key + '=' + encodeURIComponent(value);
    }
  } else {
    updatedURL = url + '?' + key + '=' + encodeURIComponent(value);
  }
  
  window.history.replaceState(null, null, updatedURL);
}

const sharing = ref({});

const networks=ref( [])

const isCanShare=ref(true)

const showModal = ref(false)

const updateShareUrl=()=>{
    
    const metaDescription = document.querySelector('meta[name="description"]');
        const descriptionContent = metaDescription ? metaDescription.content : '';

        const metaTwitterDescription = document.querySelector('meta[name="twitter:description"]');
        const twitterDescription = metaTwitterDescription ? metaTwitterDescription.content : '';
        

        const metaAuthor = document.querySelector('meta[name="author"]');
        const author = metaAuthor ? metaAuthor.content : '';
        const metaKeywords = document.querySelector('meta[name="keywords"]');
        const keywords = metaKeywords ? metaKeywords.content : '';

        const twitterCreatorDiv = document.querySelector('meta[name="twitter:creator"]');
        const twitterCreator = twitterCreatorDiv ? twitterCreatorDiv.content : ''; 

        sharing.value={
                url: window.location.href,
                title: document.title,
                description:twitterDescription||descriptionContent,
                quote:twitterDescription||descriptionContent,
                hashtags:keywords,
                twitterUser: twitterCreator||author
            }
}

const toggleModal = () => {
  showModal.value = !showModal.value


    const langsIndex={"english":"3","chinese_simplified":"4","chinese_traditional":"5","russian":"6","japanese":"7","korean":"8","deutsch":"9","spanish":"10","italian":"11","norwegian":"12","dutch":"13","filipino":"14","lao":"15","romanian":"16","nepali":"17","french":"18","haitian_creole":"19","czech":"20","swedish":"21","malagasy":"22","burmese":"23","pashto":"24","thai":"25","armenian":"26","persian":"27","kurdish":"28","turkish":"29","hindi":"30","bulgarian":"31","malay":"32","swahili":"33","oriya":"34","irish":"35","gujarati":"36","slovak":"37","hebrew":"38","hungarian":"39","marathi":"40","tamil":"41","estonian":"42","malayalam":"43","inuktitut":"44","arabic":"45","slovene":"46","bengali":"47","urdu":"48","azerbaijani":"49","portuguese":"50","samoan":"51","afrikaans":"52","greek":"53","danish":"54","amharic":"55","albanian":"56","lithuanian":"57","vietnamese":"58","maltese":"59","finnish":"60","catalan":"61","croatian":"62","bosnian":"63","polish":"64","latvian":"65","maori":"66"}
    const selectLanguage = document.getElementById("translateSelectLanguage");
    if(langsIndex[selectLanguage.value]){
        addURLParameter('lang',langsIndex[selectLanguage.value]);
        updateShareUrl()
      }

}



onMounted(async () => {


    const init=() => {
     
        updateShareUrl()
            
        const share_config = document.querySelector('meta[name="share_config"]');
        const shares = share_config ? share_config.content : ''; 
        console.log('#shareIcon', share_config)
        
        networks.value=[
          { network: 'twitter', name: 'Twitter', icon: 'fab fah fa-lg fa-twitter', color: '#1da1f2' },
          { network: 'weibo', name: 'Weibo', icon: 'fab fah fa-lg fa-weibo', color: '#e9152d' },

            { network: 'buffer', name: 'Buffer', icon: 'fab fah fa-lg fa-buffer', color: '#323b43' },
            { network: 'email', name: 'Email', icon: 'far fah fa-lg fa-envelope', color: '#333333' },
           
            { network: 'facebook', name: 'Facebook', icon: 'fab fah fa-lg fa-facebook-f', color: '#1877f2' },
            { network: 'flipboard', name: 'Flipboard', icon: 'fab fah fa-lg fa-flipboard', color: '#e12828' },
            { network: 'hackernews', name: 'HackerNews', icon: 'fab fah fa-lg fa-hacker-news', color: '#ff4000' },
            { network: 'instapaper', name: 'Instapaper', icon: 'fas fah fa-lg fa-italic', color: '#428bca' },
            { network: 'line', name: 'Line', icon: 'fab fah fa-lg fa-line', color: '#00c300' },
            { network: 'linkedin', name: 'LinkedIn', icon: 'fab fah fa-lg fa-linkedin', color: '#007bb5' },
           
            { network: 'odnoklassniki', name: 'Odnoklassniki', icon: 'fab fah fa-lg fa-odnoklassniki', color: '#ed812b' },
            { network: 'pinterest', name: 'Pinterest', icon: 'fab fah fa-lg fa-pinterest', color: '#bd081c' },
            { network: 'pocket', name: 'Pocket', icon: 'fab fah fa-lg fa-get-pocket', color: '#ef4056' },
            { network: 'quora', name: 'Quora', icon: 'fab fah fa-lg fa-quora', color: '#a82400' },
            { network: 'reddit', name: 'Reddit', icon: 'fab fah fa-lg fa-reddit-alien', color: '#ff4500' },
          
            { network: 'tumblr', name: 'Tumblr', icon: 'fab fah fa-lg fa-tumblr', color: '#35465c' },
           
            { network: 'vk', name: 'Vk', icon: 'fab fah fa-lg fa-vk', color: '#4a76a8' },
            { network: 'wordpress', name: 'Wordpress', icon: 'fab fah fa-lg fa-wordpress', color: '#21759b' },
            { network: 'xing', name: 'Xing', icon: 'fab fah fa-lg fa-xing', color: '#026466' },
            { network: 'yammer', name: 'Yammer', icon: 'fab fah fa-lg fa-yammer', color: '#0072c6' },
            
            // { network: 'whatsapp', name: 'Whatsapp', icon: 'fab fah fa-lg fa-whatsapp', color: '#25d366' },
             // { network: 'viber', name: 'Viber', icon: 'fab fah fa-lg fa-viber', color: '#59267c' },
                // { network: 'baidu', name: 'Baidu', icon: 'fas fah fa-lg fa-paw', color: '#2529d8' },
            // { network: 'evernote', name: 'Evernote', icon: 'fab fah fa-lg fa-evernote', color: '#2dbe60' },
            // { network: 'messenger', name: 'Messenger', icon: 'fab fah fa-lg fa-facebook-messenger', color: '#0084ff' },
            // { network: 'skype', name: 'Skype', icon: 'fab fah fa-lg fa-skype', color: '#00aff0' },
            // { network: 'sms', name: 'SMS', icon: 'far fah fa-lg fa-comment-dots', color: '#333333' },
            // { network: 'stumbleupon', name: 'StumbleUpon', icon: 'fab fah fa-lg fa-stumbleupon', color: '#eb4924' },
            // { network: 'telegram', name: 'Telegram', icon: 'fab fah fa-lg fa-telegram-plane', color: '#0088cc' },
            // { network: 'fakeblock', name: 'Custom Network', icon: 'fab fah fa-lg fa-vuejs', color: '#41b883' }
        ].filter(n=>{
            return shares.includes(n.network)
        })
    };

    const _checkInit=()=> setTimeout(()=>{
        const metaAuthor = document.querySelector('meta[name="author"]');
        const author = metaAuthor ? metaAuthor.content : '';

        if(author){
            init()
        }else{
            _checkInit()
        }
    }, 200);

    _checkInit()


    if(frontmatter.value.share!==undefined){
        isCanShare.value=frontmatter.value.share;
    }
    console.log('frontmatter.value.share',frontmatter.value.share,isCanShare)

})

</script>

<style>
.share_btns {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  width: 100%;
  margin-top: 10px;
}

.share_btn {
  margin: 4px;
  background: white !important;
  color: black;
  border-radius: 4px;
  border: 1px solid black;
  padding: 8px;
  display: flex;
  width: fit-content;
  justify-content: center;
  align-items: center;
}

.share_btn span {
  line-height: 0;
  margin-left: 12px;
  margin-right: 8px;
}


.floating-btn {
  position: fixed;
  bottom: 132px;
  right: 12px;
  background-color: var(--c-brand);
  color: #fff;
  border: none;
  border-radius: 50%;
  width: 52px;
  height: 52px;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 2px 5px #0000004d;
  cursor: pointer;
  font-size: 18px;
}

.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  z-index: 1000;
}

.modal {
  position: fixed;
  bottom: 100px;
  right: 10px;
  background: white;
  padding: 10px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.5);
  z-index: 1001;
  border-radius: 10px;
  /* width: 50%; */
  max-width: 350px;

  display: flex;
  flex-direction: column;
  justify-content: flex-end;
  align-items: flex-end;

}

.qr-code {
  width: 100%;
  display: flex;
  justify-content: center;
  margin-bottom: 10px;
}

.close-btn {
  width: 44px;
  height: 32px;
  border-radius: 6px;
  border: 1px solid rgb(128, 128, 128);
}

@media (max-width: 959px) {
  .floating-btn {
    transform: scale(0.75);
    transform-origin: 100% 100%;
  }

}
</style>

<template>
  <template v-if="isCanShare">

    <div v-if="showModal" class="modal-overlay" @click="toggleModal"></div>
    <div v-if="showModal" class="modal">

      <button class="close-btn" @click="toggleModal">
        <i class="fas fa-times"></i>
      </button>


      <div class="share_btns" id="share_btns">
        <ShareNetwork class="share_btn" v-for="network in networks" :network="network.network" :key="network.network"
          :style="{ backgroundColor: network.color }" :url="sharing.url" :title="sharing.title"
          :description="sharing.description" :quote="sharing.quote" :hashtags="sharing.hashtags"
          :twitterUser="sharing.twitterUser">
          <i :class="network.icon"></i>
          <span>{{ network.name }}</span>
        </ShareNetwork>

      </div>
      <div class="qr-code" style="margin-top: 10px">
        <QrcodeVue :value="sharing.url" :size="128" />
      </div>
      <!-- <button @click="toggleModal">关闭</button> -->

    </div>

    <button class="floating-btn" @click="toggleModal">
      <svg t="1717051259693" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg"
        p-id="1477" width="24" height="24">
        <path
          d="M768.704 703.616c-35.648 0-67.904 14.72-91.136 38.304l-309.152-171.712c9.056-17.568 14.688-37.184 14.688-58.272 0-12.576-2.368-24.48-5.76-35.936l304.608-189.152c22.688 20.416 52.384 33.184 85.216 33.184 70.592 0 128-57.408 128-128s-57.408-128-128-128-128 57.408-128 128c0 14.56 2.976 28.352 7.456 41.408l-301.824 187.392c-23.136-22.784-54.784-36.928-89.728-36.928-70.592 0-128 57.408-128 128 0 70.592 57.408 128 128 128 25.664 0 49.504-7.744 69.568-20.8l321.216 178.4c-3.04 10.944-5.184 22.208-5.184 34.08 0 70.592 57.408 128 128 128s128-57.408 128-128S839.328 703.616 768.704 703.616zM767.2 128.032c35.296 0 64 28.704 64 64s-28.704 64-64 64-64-28.704-64-64S731.904 128.032 767.2 128.032zM191.136 511.936c0-35.296 28.704-64 64-64s64 28.704 64 64c0 35.296-28.704 64-64 64S191.136 547.232 191.136 511.936zM768.704 895.616c-35.296 0-64-28.704-64-64s28.704-64 64-64 64 28.704 64 64S804 895.616 768.704 895.616z"
          fill="#FFFFFF" p-id="1478"></path>
      </svg>
    </button>


    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.13.0/css/all.min.css"
      integrity="sha256-h20CPZ0QyXlBuAw7A+KluUYx/3pK+c7lYEpqLTlxjYQ=" crossorigin="anonymous" />


  </template>



</template>
