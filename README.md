# create-modal-wrapper

```bash
# 确保安装 vue cli3 全局模块
$ vue serve App.vue
```

日常开发中我们经常会对 modal 进行二次封装，内部核心组件用的还是第三方的 modal，但是里面会穿插一些业务封装

假设我们封装的组件为 Modal.vue，组件向外暴露属性 `value` 控制其 显示/隐藏，modal 实际控制采用 element ui 的 dialog 控件

## step 0

App.vue:

```vue
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
```

Modal.vue:

```vue
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
  props: ['value']
}
</script>
```

这个时候发现点击按钮没生效，因为 Modal.vue 中实际控制弹窗的 dialogVisible 并没有变化

## step 1

在 Modal.vue 中，监听 value 的变化，实时改变 dialogVisible 值

```vue
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
    }
  }
}
</script>
```

依然存在问题，因为 modal 隐藏后，没有反应给最顶级的组件，即没有改变 dialogVisible 的值

## step 2

对于 element ui 的 dialog 组件来说，其本身提供了 close 钩子，可以监听到 modal 关闭时的情况，我们在 modal 关闭时将实时信息 emit 给最顶级的组件即可

```vue
<template>
  <el-dialog
    title="提示"
    :visible.sync="dialogVisible"
    width="30%"
    @close="handleClose"
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
  methods: {
    handleClose () {
      this.$emit('input', false)
    }
  },
  watch: {
    value (val) {
      this.dialogVisible = val
    }
  }
}
</script>
```

但是像 mint-ui 的 popup 组件，没有提供这样的钩子，怎么办？我们可以自己提供这样一个事件，但是这就需要修改源码了。**我们还可以监听 dialogVisible 的变化**，这是一个更通用的解决方案

```vue
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
```

