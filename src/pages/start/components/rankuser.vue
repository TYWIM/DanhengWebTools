<template>
  <div>
    <!-- 滚动公告 -->
    <div class="scrolling-notice" v-if="showNotice">
      <marquee behavior="scroll" direction="left">{{ noticeContent }}</marquee>
    </div>

    <!-- 原有的组件内容 -->
    <div class="commuse">
      <div class="commuse-item">
        <div class="text-neutral-400 dark:text-slate-100">{{ t('main.typename') }}</div>
        <a-cascader allow-search v-model="value2" :options="options" placeholder="" filterable />
      </div>
      <div class="commuse-item">
        <div class="text-slate-900 dark:text-slate-100"> 星魂: </div>
        <a-input-number v-model="grade" placeholder="请输入数量" mode="button" size="large" class="input-demo" />
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
import { reactive, ref, computed, onMounted, inject } from 'vue'
import { useClipboard } from '@vueuse/core'
import { Message } from '@arco-design/web-vue'
import { useAppStore } from '@/store/modules/app'
import axios from 'axios'
import rankuser from './json/avatar.json'
import JSEncrypt from 'jsencrypt';
import {useI18n} from "vue-i18n";

const { t, locale } = useI18n();

const API_BASE_URL = import.meta.env.VITE_DHWT_API_SERVER;

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



const { text, isSupported, copy } = useClipboard()
const appStore = useAppStore()

const value2 = ref(1001)
const grade = ref(6)


const value = computed(() => {
  return `avatar rank ${value2.value} ${grade.value}`
})

const options = reactive(rankuser)
const message = Message

function copyvalue() {
  copy(value.value)
  if (isSupported) {
    message.success(`已复制${value.value}`)
  }
}





const send = inject("send")

const showNotice = ref(true)
const noticeContent = 'PrayerTools及其他任何衍生工具都是免费软件，如果你是付费购买的，那你就被骗了，请及时退款并举报。'

// 在页面加载时设置一个延时，用于显示滚动公告，你可以根据需求调整延时时长
onMounted(() => {
  setTimeout(() => {
    showNotice.value = true
  }, 1000)
})
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
  border-radius: 10px; /* 添加圆角样式 */
}

.commuse {
  width: 500px;
  margin: auto;
}

.commuse-item {
  display: flex;
  align-items: center;
  margin: 18px 0;

  > div {
    &:nth-child(1) {
      width: 150px;
      text-align: right;
      padding-right: 10px;
      color: #6b6a6a !important;  /* 使用 !important 提高优先级 */
    }
  }
}

.generate {
  display: flex;
  align-items: center;
  margin-left: 100px;
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
