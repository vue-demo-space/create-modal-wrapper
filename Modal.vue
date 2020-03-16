<template>
  <el-dialog
    title="提示"
    :visible.sync="dialogVisible"
    width="30%"
    >
    <span>这是一段信息</span>
  </el-dialog>
</template>

<script>
export default {
  data () {
    return {
      dialogVisible: this.value
    }
  },
  props: ['value'],
  watch: {
    value (val) {
      this.dialogVisible = val
    },
    dialogVisible (val) {
      this.$emit('input', val)
    }
  }
}
</script>