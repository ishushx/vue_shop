<template>
  <div>
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>权限管理</el-breadcrumb-item>
      <el-breadcrumb-item>角色列表</el-breadcrumb-item>
    </el-breadcrumb>

    <!-- 卡片视图 -->
    <el-card>
      <!-- 添加角色按钮区 -->
      <el-row>
        <el-col>
          <el-button type="primary" @click="addDialogVisible = true"
            >添加角色</el-button
          >
        </el-col>
      </el-row>

      <!-- 列表区 -->
      <el-table :data="roleList" border stripe>
        <el-table-column type="expand"></el-table-column>
        <el-table-column type="index"></el-table-column>
        <el-table-column label="角色名称" prop="roleName"></el-table-column>
        <el-table-column label="角色描述" prop="roleDesc"></el-table-column>
        <el-table-column label="操作" width="300px">
          <template slot-scope="">
            <el-button size="mini" type="primary" icon="el-icon-edit"
              >编辑</el-button
            >
            <el-button size="mini" type="danger" icon="el-icon-delete"
              >删除</el-button
            >
            <el-button size="mini" type="warning" icon="el-icon-setting"
              >分配权限</el-button
            >
          </template>
        </el-table-column>
      </el-table>
    </el-card>

    <!-- dialog区域 -->
    <!-- 新增表单 -->
    <el-dialog
      title="添加角色"
      :visible.sync="addDialogVisible"
      width="30%"
      @close="addDialogClosed()"
    >
      <!-- 内容主体区 -->
      <el-form
        :model="addForm"
        :rules="addFormRules"
        ref="addFormRef"
        label-width="100px"
      >
        <el-form-item label=" 角色名称" prop="roleName">
          <el-input v-model="addForm.roleName"></el-input>
        </el-form-item>
        <el-form-item label=" 角色描述" prop="roleDesc">
          <el-input v-model="addForm.roleDesc"></el-input>
        </el-form-item>
      </el-form>
      <!-- footer -->
      <span slot="footer" class="dialog-footer">
        <el-button @click="addDialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="addRules()">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
// todo
// 新增,修改,删除

export default {
  data() {
    return {
      //所有角色列表数据
      roleList: [],
      addDialogVisible: false,
      addForm: {
        roleName: '',
        roleDesc: ''
      },
      addFormRules: {
        roleName: [
          {
            required: true,
            message: '请输入角色名',
            trigger: 'blur'
          },
          {
            min: 3,
            max: 10,
            message: '角色名在 3 到 10 个字符之间',
            trigger: 'blur'
          }
        ],
        roleDesc: [
          {
            message: '请输入角色描述',
            trigger: 'blur'
          },
          {
            min: 3,
            max: 10,
            message: '角色描述在 3 到 10 个字符之间',
            trigger: 'blur'
          }
        ]
      }
    }
  },
  created() {
    this.getRolesList()
  },
  methods: {
    async getRolesList() {
      const { data: res } = await this.$http.get('roles')
      if (res.meta.status !== 200) {
        return this.$message.error('获取角色列表失败')
      }
      this.roleList = res.data
      console.log(this.roleList)
    },
    addDialogClosed() {
      this.$refs.addFormRef.resetFields()
    },
    addRules() {
      this.$refs.addFormRef.validate(async valid => {
        if (!valid) return
        const { data: res } = await this.$http.post('roles', this.addForm)

        if (res.meta.status !== 201) {
          this.$message.error('添加角色失败!')
        } else {
          this.$message.success('添加角色成功')
        }
        this.addDialogVisible = false
        this.getRolesList()
      })
    }
  }
}
</script>

<style></style>
