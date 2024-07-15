<script setup>
import { computed, onMounted, onUnmounted, ref } from 'vue'

const langs={
  "3": "english",
  "4": "chinese_simplified",
  "5": "chinese_traditional",
  "6": "russian",
  "7": "japanese",
  "8": "korean",
  "9": "deutsch",
  "10": "spanish",
  "11": "italian",
  "12": "norwegian",
  "13": "dutch",
  "14": "filipino",
  "15": "lao",
  "16": "romanian",
  "17": "nepali",
  "18": "french",
  "19": "haitian_creole",
  "20": "czech",
  "21": "swedish",
  "22": "malagasy",
  "23": "burmese",
  "24": "pashto",
  "25": "thai",
  "26": "armenian",
  "27": "persian",
  "28": "kurdish",
  "29": "turkish",
  "30": "hindi",
  "31": "bulgarian",
  "32": "malay",
  "33": "swahili",
  "34": "oriya",
  "35": "irish",
  "36": "gujarati",
  "37": "slovak",
  "38": "hebrew",
  "39": "hungarian",
  "40": "marathi",
  "41": "tamil",
  "42": "estonian",
  "43": "malayalam",
  "44": "inuktitut",
  "45": "arabic",
  "46": "slovene",
  "47": "bengali",
  "48": "urdu",
  "49": "azerbaijani",
  "50": "portuguese",
  "51": "samoan",
  "52": "afrikaans",
  "53": "greek",
  "54": "danish",
  "55": "amharic",
  "56": "albanian",
  "57": "lithuanian",
  "58": "vietnamese",
  "59": "maltese",
  "60": "finnish",
  "61": "catalan",
  "62": "croatian",
  "63": "bosnian",
  "64": "polish",
  "65": "latvian",
  "66": "maori"
}

const langsIndex={"english":"3","chinese_simplified":"4","chinese_traditional":"5","russian":"6","japanese":"7","korean":"8","deutsch":"9","spanish":"10","italian":"11","norwegian":"12","dutch":"13","filipino":"14","lao":"15","romanian":"16","nepali":"17","french":"18","haitian_creole":"19","czech":"20","swedish":"21","malagasy":"22","burmese":"23","pashto":"24","thai":"25","armenian":"26","persian":"27","kurdish":"28","turkish":"29","hindi":"30","bulgarian":"31","malay":"32","swahili":"33","oriya":"34","irish":"35","gujarati":"36","slovak":"37","hebrew":"38","hungarian":"39","marathi":"40","tamil":"41","estonian":"42","malayalam":"43","inuktitut":"44","arabic":"45","slovene":"46","bengali":"47","urdu":"48","azerbaijani":"49","portuguese":"50","samoan":"51","afrikaans":"52","greek":"53","danish":"54","amharic":"55","albanian":"56","lithuanian":"57","vietnamese":"58","maltese":"59","finnish":"60","catalan":"61","croatian":"62","bosnian":"63","polish":"64","latvian":"65","maori":"66"}

//提取url里的参数
function getURLParameters(url) {
  var params = {};
  var urlParts = url.split('?');

  if (urlParts.length > 1) {
    var queryString = urlParts[1];
    var paramPairs = queryString.split('&');

    for (var i = 0; i < paramPairs.length; i++) {
      var pair = paramPairs[i].split('=');
      var paramName = decodeURIComponent(pair[0]);
      var paramValue = decodeURIComponent(pair[1] || '');

      if (paramName.length > 0) {
        params[paramName] = paramValue;
      }
    }
  }

  return params;
};

//js方法，往当前网页的url里添加参数
//如果key已经存在，则替换为新的value
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

function removeURLParameter(key) {
  var url = window.location.href;
  var updatedURL = '';

  if (url.indexOf('?') !== -1) {
    var baseURL = url.split('?')[0];
    var params = url.split('?')[1].split('&');
    var newParams = params.filter(function(param) {
      return param.split('=')[0] !== key;
    });

    if (newParams.length > 0) {
      updatedURL = baseURL + '?' + newParams.join('&');
    } else {
      updatedURL = baseURL;
    }
  } else {
    updatedURL = url;
  }

  window.history.replaceState(null, null, updatedURL);
}

onMounted(() => {

  const tr=() => {
    const {lang}=getURLParameters(window.location.href)
    console.log('#传参lang',lang)
    // 检查localStorage中是否有语言设置，如果有，则使用该设置
    window.translate.ignore.id.push('translateSelectLanguage');
    window.translate.ignore.id.push('share_btns');
    window.translate.language.clearCacheLanguage();
    window.translate.selectLanguageTag.show = false;
    let savedLanguage = localStorage.getItem('selectedLanguage');
    //优先使用url传参
    if(lang&&langs[lang]) savedLanguage=langs[lang];
    if (savedLanguage && savedLanguage!=="Not Translate" && savedLanguage!=="Default") {
      console.log("Saved language found1", savedLanguage);
      window.translate.language.setDefaultTo(savedLanguage);
      selectLanguage.value = savedLanguage;  // 设置select元素以反映当前选择的语言
    } else if(savedLanguage && savedLanguage==="Not Translate"){
      console.log("Saved language found2", savedLanguage);
      selectLanguage.value = 'Not Translate';
      //localStorage.removeItem('selectedLanguage')
      removeURLParameter('lang')
      return
    } else {
      console.log("Saved language not found");
      window.translate.setAutoDiscriminateLocalLanguage();
      selectLanguage.value = 'Default';  // 设置select元素以反映当前选择的语言
      // localStorage.removeItem('selectedLanguage')
      removeURLParameter('lang')
    }

    if(langsIndex[selectLanguage.value]){
      addURLParameter('lang',langsIndex[selectLanguage.value])
    }

    window.translate.service.use('client.edge');
    window.translate.listener.start();
    window.translate.execute(); // 进行翻译

  };

  const _tr=()=> setTimeout(()=>{
    if(window.translate){
      tr()
    }else{
      _tr()
    }
  }, 200);

  _tr()

  const selectLanguage = document.getElementById("translateSelectLanguage");
  selectLanguage.addEventListener('change', () => {
    const selectedLanguage = selectLanguage.value;
    // console.log(selectedLanguage,selectedLanguage=='Not Translate');

    if(selectedLanguage === 'Default' || selectedLanguage === 'Not Translate') {
      removeURLParameter('lang')
      // localStorage.removeItem('selectedLanguage')
      localStorage.setItem('selectedLanguage', selectedLanguage);
    } else {
      // 保存选中的语言到localStorage
      localStorage.setItem('selectedLanguage', selectedLanguage);
      if(langsIndex[selectedLanguage]){
        addURLParameter('lang',langsIndex[selectedLanguage])
      }
    }
    // 刷新页面以应用新的语言设置
    window.location.reload();
  });

})
</script>

<style lang="scss">

</style>

<template>
  <select id="translateSelectLanguage" style="margin-left: 20px;font-size: 12px;">
    <option value="Not Translate" selected="selected">Not Translate</option>
    <option value="Default">Auto Translate</option>
    <option value="english">English</option>
    <option value="chinese_simplified">简体中文</option>
    <option value="chinese_traditional">繁體中文</option>
    <option value="russian">Русский</option>
    <!-- <option value="russian">Русский язык</option> -->
    <option value="japanese">しろうと</option>
    <option value="korean">한국어</option>
    <option value="deutsch">Deutsch</option>
    <option value="spanish">Español</option>
    <option value="italian">italiano</option>
    <option value="norwegian">Norge</option>
    <option value="dutch">nederlands</option>
    <option value="filipino">Pilipino</option>
    <option value="lao">ກະຣຸນາ</option>
    <option value="romanian">Română</option>
    <!--    <option value="nepali">नेपालीName</option>-->
    <option value="french">Français</option>
    <option value="haitian_creole">Kreyòl ayisyen</option>
    <option value="czech">český</option>
    <option value="swedish">Svenska</option>
    <option value="malagasy">Malagasy</option>
    <!--    <option value="burmese">ဗာရမ်</option>-->
    <option value="pashto">پښتوName</option>
    <!--    <option value="thai">คนไทย</option>-->
    <option value="persian">Persian</option>
    <option value="kurdish">Kurdî</option>
    <option value="turkish">Türkçe</option>
    <option value="hindi">हिन्दी</option>
    <option value="bulgarian">български</option>
    <option value="malay">Malay</option>
    <option value="swahili">Kiswahili</option>
    <option value="oriya">ଓଡିଆ</option>
    <option value="irish">Íris</option>
    <option value="gujarati">ગુજરાતી</option>
    <option value="slovak">Slovenská</option>
    <option value="hebrew">היברית</option>
    <option value="hungarian">magyar</option>
    <option value="marathi">मराठीName</option>
    <option value="tamil">தாமில்</option>
    <option value="estonian">eesti keel</option>
    <option value="malayalam">മലമാലം</option>
    <!--    <option value="inuktitut">ᐃᓄᒃᑎᑐᑦ</option>-->
    <option value="arabic">بالعربية</option>
    <option value="slovene">slovenščina</option>
    <option value="bengali">বেঙ্গালী</option>
    <option value="urdu">اوردو</option>
    <option value="azerbaijani">azerbaijani</option>
    <option value="portuguese">português</option>
    <option value="samoan">lifiava</option>
    <option value="afrikaans">afrikaans</option>
    <option value="greek">ελληνικά</option>
    <option value="danish">dansk</option>
    <option value="amharic">amharic</option>
    <option value="albanian">albanian</option>
    <option value="lithuanian">Lietuva</option>
    <option value="vietnamese">Tiếng Việt</option>
    <option value="maltese">Malti</option>
    <option value="finnish">suomi</option>
    <option value="catalan">català</option>
    <option value="croatian">hrvatski</option>
    <option value="bosnian">bosnian</option>
    <option value="polish">Polski</option>
    <option value="latvian">latviešu</option>
    <option value="maori">Maori</option>
  </select>
</template>
