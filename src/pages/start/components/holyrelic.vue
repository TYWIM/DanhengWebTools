<template>
  <div>
    
    <div class="scrolling-notice" v-if="showNotice">
      <marquee behavior="scroll" direction="left">{{ noticeContent }}</marquee>
    </div>

    
    <NAlert title="Tips" type="info" closable class="custom-info-alert">
      <template #icon>
        <n-icon>
          <ios-airplane />
        </n-icon>
      </template>
      {{ t('main.tips') }}
    </NAlert>


    
    <div class="commuse">
      <div class="commuse-item">
        <div class="text-slate-900 dark:text-slate-100">{{ t('relic.relic') }}:</div>
        <a-cascader allow-search v-model="holyrelicnamevalue" :options="options" placeholder="" filterable />
      </div>

      <div class="commuse-item">
        <div class="text-slate-900 dark:text-slate-100">{{ t('relic.basestats') }}:</div>
        <a-cascader allow-search v-model="holyrelicnmainvalue" :options="options2" placeholder="" filterable />
      </div>

      <div class="commuse-item">
        <div class="text-slate-900 dark:text-slate-100">{{ t('relic.advancedstats') }}:</div>

        <div class="smallho">
          <div class="smallho-item" v-for="(item, index) in options3" :key="index">
            <a-checkbox v-model="item.isCheck"></a-checkbox>
            <div class="text-slate-900 dark:text-slate-100">{{ item.label }} </div>
            <div>
              <a-input-number placeholder="" v-model="item.num" :min="1" />
            </div>
          </div>
        </div>
      </div>

      <div class="commuse-item">
        <div class="text-slate-900 dark:text-slate-100">{{ t('relic.enhancementlevel') }}</div>

        <a-input-number placeholder="" v-model="grade" :min="0" :max="9999" />
      </div>

      <div class="generate">
        <a-input v-model="value" placeholder="" />
        <a-button type="primary" shape="round" size="large" @click="copyvalue">复制</a-button>
        <a-button type="primary" shape="round" size="large" @click="execute">执行</a-button>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, reactive, computed, onMounted, inject } from 'vue'
import { useClipboard } from '@vueuse/core'
import holyrelicname from './json/holyrelicname.json'
import holyrelicnmain from './json/holyrelicnmain.json'
import holyrelicx from './json/holyrelicnx.json'
import { Message } from '@arco-design/web-vue'
import { useAppStore } from '@/store/modules/app'
import { useI18n } from 'vue-i18n'
import { IosAirplane } from '@vicons/ionicons4'
import { NAlert } from 'naive-ui';
import axios from 'axios'
import qs from 'qs'
import JSEncrypt from 'jsencrypt';
const { t, locale } = useI18n()
const { text, isSupported, copy } = useClipboard()
const appStore = useAppStore()
const API_BASE_URL = import.meta.env.VITE_DHWT_API_SERVER;
var holyrelicnamevalue = ref('')
var holyrelicnmainvalue = ref('')
var grade = ref(0)
var modifiedValue = '';
var xct = ''
const value = computed(() => {
  var xct = ''
  options3.value.forEach((k) => {
    if (k.isCheck) {
      xct = xct + ` ${k.value}:${k.num}`
    }
  })

  // 删除第一个标识字符 a=头部 b=手部 c=躯干 d=脚部 e=位面球 f=连接绳
  const modifiedValue = holyrelicnmainvalue.value.slice(1);

  // 如果 xct 为空，则使用默认值 1
  xct = xct || ' 1';

  return `relic ${holyrelicnamevalue.value} ${modifiedValue}${xct} l${grade.value} x1`
})
const execute = async () => {
  //读取localStorage中存储的uid
  const uid = localStorage.getItem('uid');

  if (!uid) {
    message.info('用户未登录，请先前往“远程”页面执行一次命令，然后重试');
    return;
  }

  const command = value.value;

  try {
    // 发送请求到后端
    const res = await axios.post(`${API_BASE_URL}/api/submit`, {
      keyType: 'PEM',  
      uid: uid,
      command: command
    });

    if (res.data.code !== 0) {
      throw new Error('命令提交失败: ' + res.data.message);
    }
    const responseMessage = res.data.data.message;
    message.success(`命令提交成功：${res.data.data.message}`);
  } catch (err: unknown) {
    const errorMessage = err instanceof Error ? err.message : '请求失败';
    message.error(errorMessage);
    console.error(err);
  }
};

const options = reactive(holyrelicname)
const options2 = reactive(holyrelicnmain)

var holyrelicx1 = holyrelicx.map((k) => {
  const obj = {
    isCheck: false,
    num: ref(1),
    label: k.label,
    value: k.value,
  }
  return obj
})

const options3 = ref(holyrelicx1)
const message = Message

function copyvalue() {
  copy(value.value)
  if (isSupported) {
    message.success(`已复制${value.value}`)
  }
}

const send: any = inject("send")
const showNotice = ref(true)
const noticeContent = 'PrayerTools及其他任何衍生工具都是免费软件，如果你是付费购买的，那你就被骗了，请及时退款并举报。'

// 在页面加载时设置一个延时，用于显示滚动公告，你可以根据需求调整延时时长
onMounted(() => {
  // 根据浏览器语言设置初始语言
  locale.value = navigator.language.includes('zh') ? 'zh' : 'en';

  // 使用 import 动态加载 JSON 数据
  const jsonPath = locale.value ? `./json/${locale.value}/holyrelicnx.json` : './json/zh/holyrelicnx.json';

  import(jsonPath).then((holyrelicData) => {
    options3.value = holyrelicData.default;

    setTimeout(() => {
      showNotice.value = true;
    }, 1000);
  });
});
</script>

<style lang="less" scoped>
/* 添加样式以美化滚动公告 */
.scrolling-notice {
  color: #BEBEBE;
  padding: 8px;
  font-size: 14px;
  text-align: center;
  white-space: nowrap;
  overflow: hidden;
}

.commuse {
  width: 500px;
  margin: auto;
}

.commuse-item {
  display: flex;
  align-items: center;
  color: #000;
  margin: 18px 0;

  >div {
    &:nth-child(1) {
      width: 150px;
      text-align: right;
      padding-right: 10px;
      color: #6b6a6a !important;
    }
  }
}

.generate {
  display: flex;
  align-items: center;
  margin-left: 100px;
}


.smallho {
  height: 300px;
  width: 100%;
  overflow-y: auto;
  .custom-info-alert {
  width: 30px; /* 你可以根据需要调整宽度 */
  position: fixed;
  top: 42px;
  right: 12px;
  >div {
      &:nth-child(3) {
        width: 80px;
        color: #6b6a6a !important;
      }
    }
  
}

  .smallho-item {
    margin: 10px 0;
    display: flex;
    justify-content: space-between;
    align-items: center;
    width: 90%;
    

    >div {
      &:nth-child(3) {
        width: 80px;
        color: #484848 !important;
      }
    }
  }
}
@media screen and (max-width: 768px) {
  .commuse {
    width: 100%; 
    padding: 10px; 
  }

  .commuse-item {
    margin: 18px 0 10px; 
  }

  .commuse-item > div:nth-child(1) {
    width: auto; 
    text-align: left; 
    padding: 0; 
    margin-bottom: 5px; 
  }

  .generate {
    display: block; 
    margin-left: 0; 
    width: 100%; 
    margin-bottom: 80px; 
    margin-top: 10px; 
    text-align: center; 
  }
  .generate > .arco-input {
    margin-bottom: 10px; 
  }
  .generate button { 
    display: block; 
    width: 100%; 
    margin-top: 10px; 
    
  }
}
</style>
