<script setup>
import { ref } from 'vue'
import { Edit, Delete } from '@element-plus/icons-vue'
import { artGetChannelsService, artDelChannelService } from '../../api/article'
import ChannelEdit from './components/ChannelEdit.vue'
const channelList = ref([])
const loading = ref(false)
const dialog = ref()

const getChannelList = async () => {
  loading.value = true
  const res = await artGetChannelsService()
  channelList.value = res.data.data
  loading.value = false
}
getChannelList()

const onDelChannel = async (row) => {
  await ElMessageBox.confirm('你确认要删除该分类么', '温馨提示', {
    type: 'warning',
    confirmButtonText: '确认',
    cancelButtonText: '取消'
  })
  await artDelChannelService(row.id)
  ElMessage.success('删除成功')
  getChannelList()
}
const onEditChannel = (row) => {
  dialog.value.open(row)
}//笔记，在这些方法中，dialog.value.open 调用的是子组件通过 defineExpose 暴露出来的 open 方法。当用户点击 “添加分类” 或者 “编辑分类” 按钮时，父组件就会调用子组件的 open 方法，从而打开子组件的弹窗进行相应操作。
const onAddChannel = () => {
  dialog.value.open({})
}
const onSuccess = () => {
  getChannelList()
}
</script>

<template>
  <page-container title="文章分类">
    <!-- 因为#extra是具名插槽形式，有name="extra" -->
    <template #extra>
      <el-button @click="onAddChannel">添加分类</el-button>
    </template>

    <el-table v-loading="loading" :data="channelList" style="width: 100%">
      <el-table-column type="index" label="序号" width="100"></el-table-column>
      <el-table-column prop="cate_name" label="分类名称"></el-table-column>
      <el-table-column prop="cate_alias" label="分类别名"></el-table-column>
      <el-table-column label="操作" width="150">
        <!-- row 就是 channelList 的一项， $index 下标 -->
        <template #default="{ row, $index }">
          <el-button
            :icon="Edit"
            circle
            plain
            type="primary"
            @click="onEditChannel(row, $index)"
          ></el-button>
          <el-button
            :icon="Delete"
            circle
            plain
            type="danger"
            @click="onDelChannel(row, $index)"
          ></el-button>
        </template>
      </el-table-column>

      <template #empty>
        <el-empty description="没有数据"></el-empty>
      </template>
      <!-- 当 el-table 绑定的 data 数组为空（即 channelList.value 是一个空数组 []）时，就会渲染 empty 插槽内的内容，也就是显示 <el-empty description="没有数据"></el-empty> 这部分，展示一个提示用户 “没有数据” 的空状态界面。 -->
    </el-table>

    <channel-edit ref="dialog" @success="onSuccess"></channel-edit>
    <!-- 笔记，在 Vue 里，组件实例是组件的一个具体对象，它包含了组件的所有属性和方法。当你在父组件中使用子组件时，通过 ref 给子组件设置一个引用，Vue 会在挂载阶段将这个引用指向子组件的实例。这样，父组件就可以通过这个引用去操作子组件实例。
    将 dialog 这个引用绑定到 <channel-edit> 子组件上。当组件挂载完成后，dialog.value 就会指向 <channel-edit> 子组件的实例。
访问子组件实例的方法：在父组件的 onEditChannel 和 onAddChannel 方法中，通过 dialog.value.open(row) 和 dialog.value.open({}) 调用了子组件的 open 方法。这里的 dialog.value 就是子组件的实例，通过它可以调用子组件中通过 defineExpose 暴露出来的方法。 -->

<!-- success是儿子通知老爹数据修改了 -->
  </page-container>
</template>

<style lang="scss" scoped></style>
