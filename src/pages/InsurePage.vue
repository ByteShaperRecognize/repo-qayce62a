<template>
  <div class="page-container">
    <!-- 购买成功背景区 -->
    <section class="banner-section">
      <img :src="safe" />
    </section>
    <!-- 用户信息 & 投保按钮 -->
    <section class="insure-section">
      <!-- 投保按钮 -->
      <button class="insure-btn" @click="handleInsure">
        为 {{ userInfo.name }} 投保
      </button>

      <!-- 用户信息卡 -->
      <div class="user-info-card">
        <div class="user-info-card__row">
          <span>姓名: {{ userInfo.name }}</span>
          <span>手机号: {{ userInfo.phone }}</span>
        </div>
        <div class="user-info-card__row">
          <span>有无社保: {{ userInfo.hasSocial }}</span>
          <span>身份证: {{ userInfo.idCard }}</span>
        </div>
      </div>

      <!-- 保费说明 -->
      <p class="insure-section__notice">
        重疾保障每个月预估保费为xx.xx 元（实际扣费时可能存在跨年龄导致保费波动），扣费成功后保障方能生效
      </p>

      <!-- 手势引导 -->
      <div class="gesture-hint">
        <img :src="ASSETS.gestureImg" alt="" />
      </div>
    </section>

    <!-- 为什么要买重疾险 -->
    <section class="detail-section">
      <img :src="detailImg" alt="" />
    </section>

    <!-- 缴费计划 & 协议 -->
    <PaymentPlanSection
      :monthly-price="paymentInfo.monthlyPrice"
      :auto-renew="paymentInfo.autoRenew"
      @toggle-renew="paymentInfo.autoRenew = !paymentInfo.autoRenew"
    />

    <!-- 理赔说明 -->
    <ClaimSection />

    <!-- 底部免责声明 -->
    <footer class="page-footer">
      <p>产品介绍页面仅供参考，具体责任描述以保险合同为准。</p>
      <p>*本产品年度保额600万元，保障期限1年，等待期为30天，0-50岁免赔额：3万元；51-70岁免赔额4万元。</p>
      <p>版权所有©2024安行天下保险经纪（上海）有限公司</p>
      <p>沪ICP备20025152号</p>
    </footer>

    <!-- 底部固定投保按钮 -->
    <div class="bottom-bar">
      <button class="bottom-bar__btn" @click="handleInsure">
        为 {{ userInfo.name }} 投保
      </button>
      <div class="bottom-bar__indicator" />
    </div>
  </div>
</template>

<script setup lang="ts">
import { reactive } from 'vue'
import safe from '@/assets/img/reservationPrice/ansheng/banner.webp'
import detailImg from '@/assets/img/reservationPrice/ansheng/detail.webp'
import PaymentPlanSection from '../components/PaymentPlanSection.vue'
import ClaimSection from '../components/ClaimSection.vue'

// ─── 资源地址（来自 Figma MCP）─────────────────────────────
const ASSETS = {
  thumbIcon: 'https://www.figma.com/api/mcp/asset/0f500388-a5cb-478f-9729-db3572227554',
  bannerBg: 'https://www.figma.com/api/mcp/asset/e53c7430-c175-432b-9b4c-46148855fc76',
  productImg: 'https://www.figma.com/api/mcp/asset/56f6f361-8c32-41cf-b3b7-f245668fa90f',
  gestureImg: 'https://www.figma.com/api/mcp/asset/271b8ffa-1dff-46d9-8a07-fcd7ba4b84f9',
}


// ─── 状态数据 ────────────────────────────────────────────
interface UserInfo {
  name: string
  phone: string
  hasSocial: string
  idCard: string
}

const userInfo = reactive<UserInfo>({
  name: '周*杰',
  phone: '136****3832',
  hasSocial: '有',
  idCard: '342***********97',
})

interface PaymentInfo {
  monthlyPrice: string
  autoRenew: boolean
}

const paymentInfo = reactive<PaymentInfo>({
  monthlyPrice: '2.3元/月',
  autoRenew: false,
})

// ─── 事件处理 ────────────────────────────────────────────
function handleInsure(): void {
  console.log('投保按钮点击')
}
</script>

<style lang="less">
// ─── Design Tokens ────────────────────────────────────
@color-primary: #ff8a1f;
@color-primary-dark: #ff0d0d;
@color-text-main: #111111;
@color-text-secondary: #555555;
@color-text-weak: #999999;
@color-text-white: #ffffff;
@color-bg: #f8f8fa;
@color-bg-white: #ffffff;
@color-link: #2878ff;
@color-error: #f52f3e;
@color-orange-theme: #ff7f00;

@gradient-primary: linear-gradient(103.87deg, #ff8a1f 0%, #ff0d0d 100%);
@gradient-hero: linear-gradient(174.48deg, #ff5c00 17.15%, #dc1f1f 98.42%);
@gradient-gold: linear-gradient(180deg, #ffef9a 25.86%, #fff740 79.31%);

@font-pingfang: 'PingFang SC', -apple-system, BlinkMacSystemFont, sans-serif;
@font-alibaba: 'Alibaba PuHuiTi 2.0', @font-pingfang;
@font-din: 'DIN Alternate', @font-pingfang;

// ─── 全局重置 ─────────────────────────────────────────
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
  -webkit-tap-highlight-color: transparent;
}

// ─── 页面容器 ─────────────────────────────────────────
.page-container {
  font-family: @font-pingfang;
  background-color: @color-bg;
  min-height: 100vh;
  width: 100%;
  overflow-x: hidden;
  padding-bottom: calc(200px + env(safe-area-inset-bottom));
}


// ─── 产品 Banner ──────────────────────────────────────
.banner-section {
  img {
    width: 100%;
  }
}

// ─── 投保区 ───────────────────────────────────────────
.insure-section {
  position: relative;
  margin: -160px 35px 40px;
  background: @color-bg-white;
  border-radius: 12px;
  padding: 60px 29px;
  display: flex;
  flex-direction: column;
  gap: 44px;
}

.insure-btn {
  width: 600px;
  height: 120px;
  align-self: center;
  background: @gradient-primary;
  border: none;
  border-radius: 100px;
  font-family: @font-pingfang;
  font-weight: 600;
  font-size: 48px;
  color: @color-text-white;
  text-align: center;
  cursor: pointer;
  box-shadow: 0 8px 10px rgba(255, 19, 14, 0.2);
  transition: opacity 0.2s;

  &:active {
    opacity: 0.85;
  }
}

.user-info-card {
  background: #f6f6f6;
  border-radius: 24px;
  padding: 21px 16px;
  display: flex;
  flex-direction: column;
  gap: 24px;
  font-family: @font-pingfang;
  font-weight: 400;
  font-size: 24px;
  color: @color-text-secondary;
  line-height: 32px;

  &__row {
    display: flex;
    gap: 100px;
    align-items: flex-start;
  }
}

.insure-section__notice {
  font-family: @font-alibaba;
  font-weight: 400;
  font-size: 24px;
  color: #8e8e8e;
  line-height: 32px;
}

.gesture-hint {
  position: absolute;
  right: 0;
  top: 120px;
  width: 120px;
  height: 110px;
  pointer-events: none;

  img {
    width: 100%;
    height: 100%;
    object-fit: contain;
  }
}
.detail-section {
  img {
    width: 100%;
  }
}
// ─── 底部固定栏 ───────────────────────────────────────
.bottom-bar {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  background: @color-bg-white;
  box-shadow: 0 -6px 10px rgba(0, 0, 0, 0.05);
  padding: 20px 30px 20px;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 30px;
  z-index: 100;
  padding-bottom: calc(20px + env(safe-area-inset-bottom));

  &__btn {
    width: 100%;
    height: 120px;
    border: none;
    border-radius: 100px;
    background: linear-gradient(0deg, #ff8030 0%, #ff3124 100%);
    font-family: @font-alibaba;
    font-weight: 900;
    font-size: 52px;
    color: @color-text-white;
    text-align: center;
    cursor: pointer;
    box-shadow: 0 8px 10px rgba(255, 49, 36, 0.2);
    text-shadow: 0 2px 6px rgba(0, 0, 0, 0.4);
    transition: opacity 0.2s;

    &:active {
      opacity: 0.85;
    }
  }

  &__indicator {
    width: 160px;
    height: 10px;
    background: #cccccc;
    border-radius: 20px;
  }
}

// ─── 底部免责 ─────────────────────────────────────────
.page-footer {
  background: @color-bg;
  padding: 60px 35px;
  display: flex;
  flex-direction: column;
  gap: 0;
  font-family: @font-pingfang;
  font-weight: 400;
  font-size: 24px;
  color: @color-text-weak;
  text-align: center;
  line-height: 34px;

  p {
    margin-bottom: 0;
    line-height: 34px;
  }
}
</style>
