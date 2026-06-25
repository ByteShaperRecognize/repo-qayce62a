<template>
  <div class="basicPage" ref="page">
    <div class="bannerBlock">
      <img :src="zhylycylBasicBg" alt="" />
      <BasiBannerSwiper />
    </div>
    <div class="cardBlock">
      <img class="detail" :src="zhylycylBasicDetail" alt="" />
      <div class="other">
        <BasicUserLogin
          ref="userLoginRef"
          @onStepChange="onStepChange"
          @onLoginConfirm="onLoginConfirm"
        />

        <div class="next_year" v-if="step == 3">
          <div class="title">开通次年自动重新投保</div>
          <div class="btns">
            <template v-for="item in next_year_options" :key="item">
              <a :class="next_year_auto == item ? 'on' : ''" @click="() => (next_year_auto = item)">
                {{ item }}
              </a>
            </template>
          </div>
        </div>

        <div class="rules">
          <p class="top_rule">
            开通前请仔细阅读
            <a
              @click="
                () =>
                  showRuleSh({ title: '自动重新投保服务协议', filename: '易诚自动重新投保服务协议' })
              "
            >
              《自动重新投保服务协议》
            </a>
          </p>
          <Checkbox v-model="rule_checked">
            我已阅读并同意
            <a @click.stop="showRulePicker">《保险条款》</a>
            <template v-for="(item, i) in tkylRules" key="i">
              <a @click.stop="showRule(item)">《{{ item.title }}》</a>
            </template>

            <template v-for="(item, i) in shRules" :key="i">
              <a @click.stop="showRuleSh(item)">《{{ item.title }}》</a>
            </template>
            <a @click.stop="() => showCompanyRule('隐私政策')">《隐私政策》</a>
            <a @click.stop="() => showExcel({
              productCode: productCode,
              planVersion: 'YiQiBao_A'
            })">《费率表》</a>
          </Checkbox>
        </div>
      </div>
    </div>

    <BasicCopyright />
  </div>

  <BasicRulesComp />
  <BasicRulePicker ref="rulePickerRef" :columns="tkylMultiple" @on-confirm="onPickerConfirm" />

  <SubmitFailurePopup
    ref="submitFailurePopupRef"
    :productCode="ratesConfig.productCode"
    :positionCode="positionCode"
  />

  <BasicUpgradeConfirmPopup
    ref="upgradeConfirmRef"
    @confirmHealthSubmit="confirmHealthSubmit"
    @closeHealthSubmit="closeHealthSubmit"
    @showExcel="showExcel"
  />
  <!-- 费率表 -->
  <RatesPopup ref="ratesPopupRef" :productCode="ratesConfig.productCode" :planVersion="ratesConfig.planVersion" />
</template>

<script setup lang="tsx">
  import { ref, onMounted, nextTick, computed } from 'vue'
  import { useRoute } from 'vue-router'
  import { Checkbox } from 'vant'
  import { cloneDeep } from 'lodash-es'
  import { useCommonStore } from '@/store/common'
  import { shuiDiInsureSubmit } from '@/api/sd'
  import { emitter } from '@/utils/eventBus'
  import { encryptString, parseUrl } from '@/utils/common'
  import { initTrace, setTraceProductCode, setTraceOrderNo, setTraceFormApplicant, getTraceNo } from '@/utils/trace/yc/traceInit'
  import zhylycylBasicBg from '@/assets/img/simple/ycyl/ycylBasicBg.webp'
  import zhylycylBasicDetail from '@/assets/img/simple/tkyl/basic/tkyl_basic_detail.png'
  import BasicRulePicker from './basicComponents/BasicRulePicker/index.vue'
  import BasicRulesComp from './basicComponents/BasicRulesPopup/index.vue'
  import BasicUserLogin from './basicComponents/BasicUserLogin/index.vue'
  import BasicCopyright from './basicComponents/BasicCopyright/index.vue'
  import BasiBannerSwiper from './basicComponents/BasiBannerSwiper/index.vue'
  import SubmitFailurePopup from '@/components/SubmitFailurePopup/index.vue'
  import BasicUpgradeConfirmPopup from './basicComponents/BasicUpgradeConfirmPopup/index.vue'
  import RatesPopup from '@/components/ratesPopup/index.vue'
  const productCode = computed(() => {
    const product = route.params.product;
    return Array.isArray(product) ? product[0] : product;
  });
  const route = useRoute()
  const submitFailurePopupRef = ref()
  const common = useCommonStore()
  const rule_checked = ref<boolean>(false)

  const step = ref<number>(1)

  const next_year_options = ['申请开通', '暂不开通']
  const next_year_auto = ref('申请开通')

  //  协议
  const rulePickerRef = ref()

  type RuleProps = {
    title: string
    filename: string
  }

  interface ColumnsProps {
    text: string
    value: string
  }

  const tkylMultiple = [
    {
      text: '融盛财产保险股份有限公司个人意外和疾病医疗保险（互联网专属A款）',
      value: '5.融盛财产保险股份有限公司个人意外和疾病医疗保险（互联网专属A款）条款20250619',
    }
  ]

  const tkylRules = [
    {
      title: '健康告知',
      filename: '2.健康告知',
    },
    {
      title: '特别约定',
      filename: '3.特别约定-基础版',
    },
    {
      title: '产品说明及投保须知',
      filename: '1.投保须知与声明-基础版',
    },
    {
      title: '保障详情',
      filename: '保障详情-基础',
    },
    {
      title: '职业类别表',
      filename: '10.职业类别表',
    },
    {
      title: '免责声明',
      filename: '6.免责声明',
    },
    {
      title: '理赔指南',
      filename: '8.理赔指南',
    },
    {
      title: '个人信息使用授权确认书',
      filename: '7.个人信息使用授权确认书',
    }
  ]

  const shRules = [
    {
      title: '保险经纪服务委托协议-易诚保险',
      filename: '保险经纪服务委托协议-易诚保险',
    }
  ]

  const showRulePicker = () => {
    rulePickerRef.value.handleShow()
  }

  const onPickerConfirm = (val: ColumnsProps) => {
    showRule({ title: val.text, filename: val.value })
  }

  const showRule = (item: RuleProps) => {
    emitter.emit('sd_dx', {
      title: item.title,
      path: `/ycyl/base/${item.filename}.docx`,
    })
  }

  const showRuleSh = (item: RuleProps) => {
    emitter.emit('sd_dx', {
      title: item.title,
      path: `/sd_shb/${item.filename}.docx`,
    })
  }

  // 显示费率
  const ratesPopupRef = ref()
  interface ratesConfig {
    productCode: string,
    planVersion: string
  }
  const ratesConfig = ref<ratesConfig>({
    productCode: productCode.value,
    planVersion: 'YiQiBao_A'
  })
  const showExcel = (config: ratesConfig) => {
    ratesConfig.value = config
    nextTick(() => {
      ratesPopupRef.value.show_rate = true
    })
  }

  const showCompanyRule = (str: string) => {
    emitter.emit(str)
  }

  // 协议

  onMounted(() => {
    document.title = '融盛百万医疗基础版'
    // 初始化yc可回溯
    initTrace()
    // 设置productCode用于可回溯
    setTraceProductCode(productCode.value)

    // 判断三要素（身份证、姓名、手机号），如果有则直接弹出升级确认弹窗
    const { idCard, name, mobile } = route.query
    if (idCard && name && mobile) {
      submitValues.value = {
        idCard: idCard as string,
        name: name as string,
        mobile: mobile as string,
      }
      nextTick(() => {
        upgradeConfirmRef.value.handleOpen()
      })
    }
  })

  const onStepChange = val => {
    step.value = Number(val)
  }

  // 升级确认
  const upgradeConfirmRef = ref()
  const submitValues = ref<any>({})
  const submitUpgrade = ref<any>()
  const onLoginConfirm = (values: any) => {
    submitValues.value = values
    upgradeConfirmRef.value.handleOpen()
  }
  // 点击确认优化
  const confirmHealthSubmit = () => {
    submitUpgrade.value = 'direct'
    fetchOnLoginConfirm()
  }
  // 暂不优化
  const closeHealthSubmit = () => {
    submitUpgrade.value = 'indirect'
    fetchOnLoginConfirm()
  }
  const positionCode = ref('short_return')
  const fetchOnLoginConfirm = async () => {
    const values = submitValues.value
    let usr = cloneDeep(values)
    // 设置表单投保人信息用于可回溯
    setTraceFormApplicant({ name: usr?.name, idCard: usr?.idCard, mobile: usr?.mobile })
    const targetUrl = `/pay/v1` 
    const productUrl = route.path.split('/').slice(0, 3).join('/');
    let params = {
      channelParams: route.query.channelParams || '',
      successUrl: targetUrl,
      failUrl: targetUrl,
      auditType: 1, // 投放版本
      user: usr,
      productUrl: productUrl,
      // 预约单钩子新增字段
      productCode: route.params.product, // 产品编码（魔方/预约单钩子）
      planVersion: '', // 产品方案版本-暂时为空
      retailProductCode: 'ycyqbbwylsjkb001', // 产品编码（预约单正价）
      orderNo: '', // 水滴（魔方/预约单钩子）订单号
      repeatCode: route.query.repeatCode || '', // 复购编码（魔方）-暂时为空
      orderType: 1, // 订单类型，1-系统订单，2-克隆订单
      retailType: submitUpgrade.value === 'direct' ? 1 : 0, // 是否优化
      tracBackId: getTraceNo()
    }

    localStorage.setItem('user_info', JSON.stringify(usr))

    let res = await shuiDiInsureSubmit(params)
    if (res?.code == 0 && res?.data && res?.data != '') {
      const url = parseUrl(res?.data)
      const orderStr = url?.params?.orderNo || ''
      setTraceOrderNo(orderStr)
      setTimeout(() => {
        window.location.href = res.data
      }, 500)
    } else {
      positionCode.value = 'underwrit_failure'
      submitFailurePopupRef.value.handleShow()
    }
  }
</script>

<style lang="less" scoped>
  @import url(./basic.less);

  .top_rule {
    margin-bottom: 20px;
  }
</style>
