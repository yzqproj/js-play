<template>
  <pop-window :open="cid !== ''" @close="close()">
    <div class="pop-share">
      <p class="hint">请复制下面代码粘贴到需要的地方</p>

      <textarea v-model="code" class="box-code" ref="shareCodeRef">
      </textarea>
      <p class="error" v-show="isCopy">复制好了\(^o^)/~</p>
      <button class="btn-positive" @click="copyCode()">点击复制</button>
    </div>
  </pop-window>
</template>

<script setup>
import PopWindow from './PopWindow.vue'
import {computed, reactive, ref, toRefs, watch} from "vue";
import {useStore} from "vuex";
let store =useStore()
const emit = defineEmits(['close'])
let state = reactive({
  isCopy: false,
  share:''
})
let {isCopy,share} = toRefs(state)
let props = defineProps({
  cid: String
})
let shareCode = ref()

function close() {
  emit('close')
}

function copyCode() {
  shareCode.value.select()
  navigator.clipboard.writeText(shareCode.value).then((res) => {
    console.log(res)
  })
  state.isCopy = true
}

watch(props.cid, (newVal) => {
  if (newVal !== '') {
    state.isCopy = false
  }
})
let code = computed(() => {
   store.dispatch('codeDetail',props.cid).then((res) => {
    state.share=res
  })
  return `${JSON.stringify(state.share.content) }`
})

</script>

<style scoped>
.pop-share {
  width: 400px;
  font-family: 微软雅黑;
  text-align: center;
  padding: 10px;
}

.hint {
  color: #888;
  font-size: 14px;
}

.box-code {
  width: 380px;
  max-width: 380px;
  height: 150px;
  border: 1px solid #777;
  background-color: transparent;
  outline: none;
  padding: 5px;
  box-sizing: border-box;
  color: #CCC;
}

.btn-positive {
  height: 35px;
  width: 100px;
  margin: 10px 0 10px 10px;
  box-sizing: border-box;
  border: 1px solid #777;
  background-color: #0C1021;
  color: #777;
  cursor: pointer;
  outline: none;
}

.btn-positive:active {
  background-color: #222;
}

.error {
  font-size: 12px;
  color: red;
  font-weight: bolder;
  margin: 5px 0;
  text-align: center;
}
</style>
