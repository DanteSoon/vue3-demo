<template>
  <div class="root-con1">第一个根元素 </div>
  <div class="root-con2">第二个根元素 多个根元素 便于代码组织结构化</div>
  <!-- <div class="main"><div class="text">111</div></div> -->
  <div>访问前端页面成功vue开始加载时间{{ moment(data.demoStoreData.appStartTimer).format('YYYY-MM-DD HH:mm:ss.SSS') }}</div>
  <div>页面首次渲染结束时间{{ moment(data.pageMountedTime).format('YYYY-MM-DD HH:mm:ss.SSS') }}</div>
  <div>开始载入vue项目到首次渲染结束总耗时{{ moment.duration(moment(data.pageMountedTime).valueOf()- moment(data.demoStoreData.appStartTimer).valueOf()).as('milliseconds') }}毫秒</div>
  <div>1000条数据重新渲染耗时{{ data.pageUpdateDuration }}毫秒</div>
  <button @click="changeCount"
    >点击重渲染</button
  >
  <div>
    <p v-for="item in data.forList" :key="item.id">
    {{ item.id }}/{{ item.name }}
    </p>
  </div>
</template>

<script setup>
import {
  reactive, computed, onBeforeMount, onMounted, onBeforeUpdate, onUpdated, getCurrentInstance, inject,
} from 'vue'
import { useStore } from 'vuex'
import moment from 'moment'

const store = useStore()
const data = reactive({
  storeData: computed(() => store.state.testModule),
  demoStoreData: computed(() => store.state.demoModule),
  pageMountedTime: null,
  pageBeforeUpdateTime: null,
  pageUpdatedTime: null,
  pageUpdateDuration: 0,
  forList: [],
  testList: [],
  testList2: [],
  calFlag: false,
}) // storeData为testModule的store的state

const { ctx } = getCurrentInstance()

onBeforeMount(async() => {
})
onMounted(async() => {
  data.pageMountedTime = moment()
  const tmpList = []
  const tmpList2 = []
  for (let index = 0; index < 10000; index++) {
    tmpList.push({
      id: index + 10000,
      name: `${index}啦`,
    })
    tmpList2.push({
      id: index + 20000,
      name: `${index}啦啦`,
    })
  }
  data.testList = tmpList
  data.testList2 = tmpList2
  data.forList = data.testList
})
onBeforeUpdate(() => {
  console.log('onBeforeUpdate')
  if (data.calFlag) {
    data.pageBeforeUpdateTime = moment()
  }
})
onUpdated(() => {
  console.log('onUpdated')
  if (data.calFlag) {
    data.pageUpdatedTime = moment()
    console.log('耗时', moment.duration(moment(data.pageUpdatedTime).valueOf() - moment(data.pageBeforeUpdateTime).valueOf()).as('milliseconds'))
    data.pageUpdateDuration = moment.duration(moment(data.pageUpdatedTime).valueOf() - moment(data.pageBeforeUpdateTime).valueOf()).as('milliseconds')
    data.calFlag = false
  }
  // ctx.$forceUpdate()
})
const changeCount = () => {
  // store.commit('testModule/increment') // 调用mutations
  console.log(data.forList == data.testList)
  if (data.forList == data.testList) {
    data.forList = data.testList2
  } else {
    data.forList = data.testList
  }
  data.calFlag = true
}
</script>
<style scoped lang='scss'>
.main {
  .text {
    color: red;
  }
}
</style>
