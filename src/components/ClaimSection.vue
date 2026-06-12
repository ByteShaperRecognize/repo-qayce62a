<template>
  <section class="claim-section">
    <SectionTitle title="理赔说明" />
    <!-- 理赔步骤图 -->
    <div class="claim-steps">
      <div class="claim-steps__icons">
        <template v-for="(step, i) in claimSteps" :key="i">
          <div class="claim-steps__item">
            <div class="claim-step-icon">
              <img :src="step.icon" :alt="step.label" />
            </div>
          </div>
          <img
            v-if="i < claimSteps.length - 1"
            :src="ASSETS.arrowIcon"
            class="claim-steps__arrow"
            alt="→"
          />
        </template>
      </div>
      <div class="claim-steps__labels">
        <span
          v-for="(step, i) in claimSteps"
          :key="i"
          class="claim-steps__label"
        >
          {{ step.label }}
        </span>
      </div>
    </div>

    <!-- 理赔渠道 -->
    <div class="claim-channels">
      <div class="claim-channels__dots">
        <span class="dot" />
        <span class="dot" />
        <span class="dot" />
      </div>
      <div class="claim-channels__text">
        <p>拨打安盛天平全国统一服务热线 95550；</p>
        <p>
          登录安盛天平官网
          <a class="claim-channels__link" href="https://www.axa.cn" target="_blank">www.axa.cn</a>
          在线提交理赔、保全申请；
        </p>
        <p>通过安盛天平官方微信公众号 "安盛天平保险" 在线提交理赔、保全申请。</p>
      </div>
    </div>
  </section>
</template>

<script setup lang="ts">
import SectionTitle from './SectionTitle.vue'

interface ClaimStep {
  icon: string
  label: string
}

const ASSETS = {
  arrowIcon: 'https://www.figma.com/api/mcp/asset/7803aa52-a9e4-4780-909f-38e10402b8f0',
  dotsIcon: 'https://www.figma.com/api/mcp/asset/4ab8dce7-de3f-47bb-96a6-8548071f3c89',
}

const claimSteps: ClaimStep[] = [
  {
    icon: 'https://www.figma.com/api/mcp/asset/730d0ab6-b772-4874-a97d-41446e2d0067',
    label: '提交申请',
  },
  {
    icon: 'https://www.figma.com/api/mcp/asset/77d1cbfc-ce84-4d4c-8b87-fdc898aef8a1',
    label: '理赔审核',
  },
  {
    icon: 'https://www.figma.com/api/mcp/asset/39a7ceb2-69b9-48c1-80e3-5ce744dfa5f4',
    label: '极速打款',
  },
]
</script>

<style lang="less">
@font-pingfang: 'PingFang SC', sans-serif;
@color-text-weak: #999999;
@color-link: #2878ff;

// 理赔步骤
.claim-steps {
  display: flex;
  flex-direction: column;
  gap: 7px;

  &__icons {
    display: flex;
    align-items: center;
  }

  &__item {
    flex: 1;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  &__arrow {
    width: 21px;
    height: 20px;
    flex-shrink: 0;
    object-fit: contain;
  }

  &__labels {
    display: flex;
    align-items: center;
  }

  &__label {
    font-family: @font-pingfang;
    font-weight: 400;
    font-size: 24px;
    color: #494949;
    line-height: 34px;
    text-align: center;
    flex: 1;
  }
}

.claim-step-icon {
  width: 90px;
  height: 90px;

  img {
    width: 100%;
    height: 100%;
    object-fit: contain;
  }
}

// 理赔渠道
.claim-channels {
  display: flex;
  align-items: flex-start;
  gap: 5px;

  &__dots {
    display: flex;
    flex-direction: column;
    gap: 34px;
    padding-top: 10px;
    flex-shrink: 0;
  }

  .dot {
    width: 11px;
    height: 11px;
    background: #FF7F00;
    border-radius: 50%;
  }

  &__text {
    flex: 1;

    p {
      font-family: @font-pingfang;
      font-weight: 400;
      font-size: 24px;
      color: @color-text-weak;
      line-height: 34px;
    }
  }

  &__link {
    color: @color-link;
    text-decoration: underline;
  }
}
</style>
