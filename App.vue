<template>
  <div>
    <el-button type="text" @click="dialogVisible = true">点击打开 Dialog</el-button>
    <Modal v-model="dialogVisible"/>
  </div>
</template>

<script>
import Modal from './Modal'
import Vue from 'vue';
import ElementUI from 'element-ui';
import 'element-ui/lib/theme-chalk/index.css';

Vue.use(ElementUI);

export default {
  data () {
    return {
      dialogVisible: false
    }
  },
  components: {
    Modal
  }
}
</script>