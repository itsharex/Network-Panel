<template>
  <div class="radius" :style="{ borderRadius: `var(--el-border-radius-round)` }" style="max-width: 800px; height: fit-content; display: block; margin: 0 auto; background-color: #ffffff; padding: 2%">
    <div style="text-align: center;" v-loading="!(info.globalInfo || info.localInfo || info.globalLay || info.localLay)">
      <!-- 全球信息 -->
      <transition name="el-fade-in">
        <div v-loading="!info.globalInfo" v-if="info.localInfo && !(info.globalInfo && info.localInfo['ip'] == info.globalInfo['ip'])">
          <div>
            <!-- 显示全球信息 -->
            <el-tag style="width: 50px;" class="ml-2" type="success">{{ info.globalLay ? info.globalLay + "ms" : "-ms" }}</el-tag>
            <el-text style="white-space: nowrap; vertical-align: -3px;" class="font-background">{{ info.globalInfo ? CountryCode[info.globalInfo['country_code']] : "" }}</el-text>
            <el-text style="white-space: nowrap; vertical-align: -3px;" class="font-background">{{ info.globalInfo ? info.globalInfo['isp'] : "" }}</el-text>
            <!-- 显示全球IP地址 -->
            <el-text style="white-space: nowrap; vertical-align: -3px;" class="font-background">{{ info.globalInfo ? info.globalInfo['ip'] : "" }}</el-text>
          </div>
        </div>
      </transition>
      
      <!-- 本地信息 -->
      <div v-if="info.localInfo">
        <div style="margin-top: 2px;"> <!-- 添加上下2px的间距 -->
          <!-- 显示本地信息 -->
          <el-tag style="width: 50px;" class="ml-2" type="success">{{ info.localLay ? info.localLay + "ms" : "-ms" }}</el-tag>
          <el-text style="white-space: nowrap; vertical-align: -1px;" class="font-background">{{ info.localInfo ? info.localInfo['province'] + ' ' + info.localInfo['city'] + ' ' + info.localInfo['distinct'] + ' ' + info.localInfo['isp'] : "Loading..." }}</el-text>
          <!-- 显示本地IP地址 -->
          <el-text style="white-space: nowrap; vertical-align: -1px;" class="font-background">{{ info.localInfo ? info.localInfo['ip'] : "" }}</el-text>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import CountryCode from "../assets/CountryCode.json"
import { ref, onMounted } from 'vue'

const info = ref({
  localInfo: null,
  globalInfo: null,
  localLay: 0,
  globalLay: 0,
})

onMounted(async () => {
  try {
    const responseLocal = await fetch('https://forge.speedtest.cn/api/location/info', { referrerPolicy: 'no-referrer' });
    info.value.localInfo = await responseLocal.json();

    const responseGlobal = await fetch('https://api-ipv4.ip.sb/geoip', { referrerPolicy: 'no-referrer' });
    info.value.globalInfo = await responseGlobal.json();
  } catch (error) {
    info.value.localInfo = null;
    info.value.globalInfo = null;
  }

  setTimeout(getLocalLay, info.value.localInfo ? 5000 : 1000);
  setTimeout(getGlobalLay, 1000);
});

const getLocalLay = async () => {
  try {
    var start_timestamp = new Date().getTime();
    await fetch('https://connectivitycheck.platform.hicloud.com/generate_204', { method: "HEAD", cache: "no-store", mode: 'no-cors', referrerPolicy: 'no-referrer' });
    info.value.localLay = new Date().getTime() - start_timestamp;
  } catch (error) {
    info.value.localLay = 0;
  }
}

const getGlobalLay = async () => {
  try {
    var start_timestamp = new Date().getTime();
    await fetch('https://cp.cloudflare.com/', { method: "HEAD", cache: "no-store", mode: 'no-cors', referrerPolicy: 'no-referrer' });
    info.value.globalLay = new Date().getTime() - start_timestamp;
  } catch (error) {
    info.value.globalLay = 0;
  }
}
</script>

<style>
.font-background {
  color: #344357;
  font-size: 14px;
}
</style>
