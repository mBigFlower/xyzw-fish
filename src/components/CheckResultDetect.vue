<template>
  <div class="root">
    <div class="childDiv">
      <div class="title">金砖消耗情况</div>
      <div style="margin-bottom: 6px;">{{ currentJinZhuan }}</div>
      <a-slider :model-value="currentJinZhuan" :step="10000" direction="vertical" style="height: 600px" :max="500000"
        :marks="jinZhuanMarks" @change="onJinZhuanChanged" />
    </div>
    <div class="childDiv">
      <div class="title">捕获消耗情况</div>
      <div style="margin-bottom: 6px;">{{ currentBuHuo }}</div>
      <a-slider :model-value="currentBuHuo" :step="25" direction="vertical" style="height: 600px" :max="1750"
        :marks="buHuoMarks" @change="onBuHuoChanged" />
    </div>
  </div>
</template>

<script setup>
import { JinZhuanInfo, BuHuoInfo } from './../xyzw_fish'
import { ref } from 'vue'

const props = defineProps({
  currentJinZhuan: {
    type: Number,
    default: 0,
  },
  currentBuHuo: {
    type: Number,
    default: 0,
  },
});

const jinZhuanMarks = { 0: 0 };
JinZhuanInfo.forEach(item => {
  jinZhuanMarks[item.level] = `${item.level / 10000}w - ${item.prop}道具`;
})
const buHuoMarks = { 0: 0 };
BuHuoInfo.forEach(item => {
  buHuoMarks[item.level] = `${item.level} - ${item.prop}道具`;
})

const emit = defineEmits(['update:currentJinZhuan', 'update:currentBuHuo']);
function onJinZhuanChanged(value) {
  // emit('update:currentJinZhuan', value);
}
function onBuHuoChanged(value) {
  // emit('update:currentBuHuo', value);
}

</script>
<style scoped>
.root {
  display: flex;
}

.childDiv {
  flex: 1;
}

.title {
  font-size: 16px;
  font-weight: bold;
}
</style>