<script setup>
import { ref } from 'vue'
import { artEditChannelService, artAddChannelService } from '@/api/article.js'
const dialogVisible = ref(false)
const formRef = ref()
// const formRef = ref()
// 这行代码在 <script setup> 里借助 ref 函数创建了一个响应式引用对象 formRef。初始时，formRef 的值为 undefined，后续会被赋值为特定的 DOM 元素或者组件实例。
// ref="formRef"
// 在 <template> 部分，ref="formRef" 被添加到 <el-form> 组件上。这意味着当组件完成挂载后，Vue 会自动把 <el-form> 组件的实例赋值给 formRef 的 value 属性。如此一来，你就能在 <script setup> 里通过 formRef.value 访问 <el-form> 组件的实例，进而调用该组件所提供的方法。
const formModel = ref({
  cate_name: '',
  cate_alias: ''
})
const rules = {
  cate_name: [
    { required: true, message: '请输入分类名称', trigger: 'blur' },
    {
      pattern: /^\S{1,10}$/,
      message: '分类名必须是 1-10 位的非空字符',
      trigger: 'blur'
    }
  ],
  cate_alias: [
    { required: true, message: '请输入分类别名', trigger: 'blur' },
    {
      pattern: /^[a-zA-Z0-9]{1,15}$/,
      message: '分类名必须是 1-15 位的字母或数字',
      trigger: 'blur'
    }
  ]
}

const emit = defineEmits(['success'])
const onSubmit = async () => {
  await formRef.value.validate()
  // 在上述代码中，formRef.value.validate() 调用了 <el-form> 组件的 validate 方法，该方法会对表单数据进行验证，并且返回一个 Promise。通过 await 关键字等待验证结果，只有在验证通过之后，才会继续执行后续的操作。
  const isEdit = formModel.value.id
  if (isEdit) {
    await artEditChannelService(formModel.value)
    ElMessage.success('编辑成功')
  } else {
    await artAddChannelService(formModel.value)
    ElMessage.success('添加成功')
  }
  dialogVisible.value = false
  emit('success')//const emit = defineEmits(['success']) 和 emit('success') 共同实现了子组件向父组件的通信，当子组件完成文章分类的添加或编辑操作后，会触发 success 事件通知父组件，父组件可以根据这个事件更新界面或执行其他操作。
}

// 组件对外暴露一个方法 open，基于open传来的参数，区分添加还是编辑
// open({})  => 表单无需渲染，说明是添加
// open({ id, cate_name, ... })  => 表单需要渲染，说明是编辑
// open调用后，可以打开弹窗
const open = (row) => {
  dialogVisible.value = true
  formModel.value = { ...row } // 添加 → 重置了表单内容，编辑 → 存储了需要回显的数据
}

// 向外暴露方法
defineExpose({
  open
})
</script>

<template>
  <el-dialog
    v-model="dialogVisible"
    :title="formModel.id ? '编辑分类' : '添加分类'"
    width="30%"
  >
    <el-form
      ref="formRef"
      :model="formModel"
      :rules="rules"
      label-width="100px"
      style="padding-right: 30px"
    >
      <el-form-item label="分类名称" prop="cate_name">
        <el-input
          v-model="formModel.cate_name"
          placeholder="请输入分类名称"
        ></el-input>
      </el-form-item>
      <el-form-item label="分类别名" prop="cate_alias">
        <el-input
          v-model="formModel.cate_alias"
          placeholder="请输入分类别名"
        ></el-input>
      </el-form-item>
    </el-form>
    <template #footer>
      <span class="dialog-footer">
        <el-button @click="dialogVisible = false">取消</el-button>
        <el-button type="primary" @click="onSubmit"> 确认 </el-button>
      </span>
    </template>
  </el-dialog>
</template>
