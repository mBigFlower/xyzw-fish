<template>
  <h1>本站对拿鱼结果概不负责</h1>
  <h1>仅供参考，请谨慎使用</h1>
  <div>（请根据实际情况填写下列内容，点击计算查看结果）</div>
  <a-form ref="formRef" :model="form" layout="vertical" style="margin-top: 100px">
    <a-row :gutter="16">
      <a-col :span="8">
        <a-form-item field="zhaoMu" tooltip="已使用招募" label="已使用招募" required>
          <a-input-number v-model="form.zhaoMu" placeholder="已使用招募" />
        </a-form-item>
      </a-col>
      <a-col :span="8">
        <a-form-item field="yanGuan" tooltip="已收取盐罐" label="已收取盐罐" required>
          <a-input-number v-model="form.yanGuan" placeholder="已收取盐罐" />
        </a-form-item>
      </a-col>
      <a-col :span="8">
        <a-form-item field="baoXiang" tooltip="已开宝箱分" label="已开宝箱分" required>
          <a-input-number v-model="form.baoXiang" placeholder="已开宝箱分" />
        </a-form-item>
      </a-col>
    </a-row>
    <a-row :gutter="16">
      <a-col :span="12">
        <a-form-item field="jinZhuan" tooltip="已使用金砖" label="已使用金砖" required>
          <a-input-number v-model="form.jinZhuan" placeholder="已使用金砖" />
        </a-form-item>
      </a-col>
      <a-col :span="12">
        <a-form-item field="buHuo" tooltip="已使用金鱼竿" label="已使用金鱼竿" required>
          <a-input-number v-model="form.buHuo" placeholder="已使用金鱼竿" />
        </a-form-item>
      </a-col>
    </a-row>
    <a-row :gutter="16">
      <a-col :span="12">
        <a-form-item field="goldProp" tooltip="已获得金道具" label="已获得金道具" required>
          <a-input-number v-model="form.goldProp" placeholder="已获得金道具" />
        </a-form-item>
      </a-col>
      <a-col :span="12">
        <a-form-item field="normalProp" tooltip="剩余普通道具" label="剩余普通道具" required>
          <a-input-number v-model="form.normalProp" placeholder="剩余普通道具" />
        </a-form-item>
      </a-col>
    </a-row>

    <a-form-item>
      <a-button type="primary" @click="onCalcClick">计算</a-button>
      <a-button v-if="isCalced" type="outline" style="margin-left: 10px" @click="onDetailClick">详情</a-button>
      <a-button style="margin-left: 10px" @click="onResetClick">重置</a-button>
      <span v-if="isCalced" class="simple-result">{{ statusResult?.[4].label?.slice(1) }} : {{ statusResult?.[4].value
        }}</span>
    </a-form-item>
  </a-form>
  <ResultDetect ref="resultDetectRef" v-model:currentJinZhuan="form.jinZhuan" v-model:currentBuHuo="form.buHuo" />
  <a-modal v-model:visible="visible" width="auto" @ok="handleOk" @cancel="handleCancel">
    <template #title>
      详情
    </template>
    <div class="result-layout">
      <a-descriptions :data="infoResult" :column="1" bordered />
      <a-descriptions class="status-result" :data="statusResult" :column="1" bordered />
    </div>
  </a-modal>
</template>

<script setup>
import { ref, reactive, computed } from 'vue'
import ResultDetect from './CheckResultDetect.vue'
import { calc } from '../xyzw_fish.js'

const formRef = ref(null)
const resultDetectRef = ref(null)

//#region 信息填写
const baseParams = {
  jinZhuan: 420837,
  buHuo: 1600,
  zhaoMu: 4000,
  yanGuan: 60,
  baoXiang: 100000,
  goldProp: undefined,
  normalProp: 0
}
const form = reactive({ ...baseParams })

function initInfos() {
  // 创建一个 URL 对象
  const urlObj = new URL(location.href);
  // 使用 URLSearchParams 获取查询参数
  const params = new URLSearchParams(urlObj.search);
  if (!params?.size) {
    return console.warn('no url params')
  }
  // 创建一个对象来存储参数和值
  const result = {};
  // 遍历所有参数并存储到结果对象中
  params.forEach((value, key) => {
    result[key] = +value || undefined;
  });
  Object.assign(form, result)
}

initInfos();

function onCalcClick() {
  formRef.value.validate();
  for (let key in form) {
    if (form[key] === undefined) {
      return;
    }
  }
  const res = calc(form)
  console.log('onCalcClick', form);

  infoResult.value = [...res.infoResult]
  statusResult.value = [...res.statusResult]
}

const isCalced = computed(() => {
  return infoResult.value.length > 0 && statusResult.value.length > 0
})

function onDetailClick() {
  visible.value = true
}

function onResetClick() {
  initInfos();
  infoResult.value = []
  statusResult.value = []
}
//#endregion

//#region 计算结果
const visible = ref(false)
const infoResult = ref([])
const statusResult = ref([])
function handleOk() {
  visible.value = false
}

function handleCancel() {
  visible.value = false
}
//#endregion
</script>

<style>
.read-the-docs {
  color: #888;
}


.result-layout {
  display: flex;
  margin-top: 20px;
  overflow: auto;
  max-height: 600px
}

.status-result {
  margin-left: 20px
}

.simple-result {
  margin-left: 10px;
  color: red;
  font-weight: bold;
}

@media (max-width: 768px) {
  .result-layout {
    flex-direction: column;
  }

  .status-result {
    margin-left: 0;
    margin-top: 20px;
  }
}
</style>
