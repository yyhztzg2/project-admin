<template>
<div>
  <br>
  <el-form :inline="true">
          <el-form-item label="bookname">
<el-input v-model="searchMap.bookname" placeholder="bookname"></el-input></el-form-item>
          <el-form-item label="bookbuyer">
<el-input v-model="searchMap.bookbuyer" placeholder="bookbuyer"></el-input></el-form-item>
          <el-form-item label="bookborrower">
<el-input v-model="searchMap.bookborrower" placeholder="bookborrower"></el-input></el-form-item>
          <el-form-item label="bookStatus">
<el-input v-model="searchMap.bookStatus" placeholder="bookStatus"></el-input></el-form-item>
          <el-form-item label="locationType">
<el-input v-model="searchMap.locationType" placeholder="locationType"></el-input></el-form-item>
          <el-form-item label="selectValue">
<el-input v-model="searchMap.selectValue" placeholder="selectValue"></el-input></el-form-item>
          <el-form-item label="cmspageId">
<el-input v-model="searchMap.cmspageId" placeholder="cmspageId"></el-input></el-form-item>
          <el-form-item label="bookvolume">
<el-input v-model="searchMap.bookvolume" placeholder="bookvolume"></el-input></el-form-item>

    <el-button type="primary" @click="fetchData()">查询</el-button>
    <el-button type="primary" @click="handleEdit('')">新增</el-button>
  </el-form>
  <el-table
    :data="list"
    border
    style="width: 100%">
          <el-table-column prop="pageId" label="pageId" width="80"></el-table-column>
          <el-table-column prop="bookname" label="bookname" width="80"></el-table-column>
          <el-table-column prop="bookbuyer" label="bookbuyer" width="80"></el-table-column>
          <el-table-column prop="bookborrower" label="bookborrower" width="80"></el-table-column>
          <el-table-column prop="bookStatus" label="bookStatus" width="80"></el-table-column>
          <el-table-column prop="locationType" label="locationType" width="80"></el-table-column>
          <el-table-column prop="selectValue" label="selectValue" width="80"></el-table-column>
          <el-table-column prop="cmspageId" label="cmspageId" width="80"></el-table-column>
          <el-table-column prop="bookvolume" label="bookvolume" width="80"></el-table-column>

    <el-table-column
      fixed="right"
      label="操作"
      width="100">
      <template slot-scope="scope">
        <el-button @click="handleEdit(scope.row.id)" type="text" size="small">修改</el-button>
        <el-button @click="handleDelete(scope.row.id)" type="text" size="small">删除</el-button>
      </template>
    </el-table-column>
  </el-table>
      <el-pagination
      @size-change="fetchData"
      @current-change="fetchData"
      :current-page="currentPage"
      :page-sizes="[5,10,20]"
      :page-size="10"
      layout="total, sizes, prev, pager, next, jumper"
      :total="total">
    </el-pagination>  
  <el-dialog title="编辑" :visible.sync="dialogFormVisible">
    <el-form label-width="80px">
        <el-form-item label="bookname"><el-input v-model="pojo.bookname"></el-input></el-form-item>
        <el-form-item label="bookbuyer"><el-input v-model="pojo.bookbuyer"></el-input></el-form-item>
        <el-form-item label="bookborrower"><el-input v-model="pojo.bookborrower"></el-input></el-form-item>
        <el-form-item label="bookStatus"><el-input v-model="pojo.bookStatus"></el-input></el-form-item>
        <el-form-item label="locationType"><el-input v-model="pojo.locationType"></el-input></el-form-item>
        <el-form-item label="selectValue"><el-input v-model="pojo.selectValue"></el-input></el-form-item>
        <el-form-item label="cmspageId"><el-input v-model="pojo.cmspageId"></el-input></el-form-item>
        <el-form-item label="bookvolume"><el-input v-model="pojo.bookvolume"></el-input></el-form-item>

        <el-button type="primary" @click="handleSave()">保存</el-button>
        <el-button @click="dialogFormVisible = false" >关闭</el-button>
    </el-form>
  </el-dialog>
</div>
</template>
<script>
import pageApi from '@/api/page'
export default {
  data() {
    return {
      list: [],
      total: 0, // 总记录数
      currentPage: 1, // 当前页
      pageSize: 10, // 每页大小
      searchMap: {}, // 查询条件
      dialogFormVisible: false, // 编辑窗口是否可见
      pojo: {}, // 编辑表单绑定的实体对象
      cityList: [], // 城市列表
      id: '' // 当前用户修改的ID
    }
  },
  created() {
    this.fetchData()
  },
  methods: {
    fetchData() {
      pageApi.search(this.currentPage, this.pageSize, this.searchMap).then(response => {
        this.list = response.data.rows
        this.total = response.data.total
      })
    },
    handleSave() {
      pageApi.update(this.id, this.pojo).then(response => {
        this.$message({
          message: response.message,
          type: (response.flag ? 'success' : 'error')
        })
        if (response.flag) { // 如果成功
          this.fetchData() // 刷新列表
        }
      })
      this.dialogFormVisible = false // 关闭窗口
    },
    handleEdit(id) {
      this.id = id
      this.dialogFormVisible = true // 打开窗口
      if (id !== '') { // 修改
        pageApi.findById(id).then(response => {
          if (response.flag) {
            this.pojo = response.data
          }
        })
      } else {
        this.pojo = {} // 清空数据
      }
    },
    handleDelete(id) {
      this.$confirm('确定要删除此纪录吗?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        pageApi.deleteById(id).then(response => {
          this.$message({ message: response.message, type: (response.flag ? 'success' : 'error') })
          if (response.flag) {
            this.fetchData() // 刷新数据
          }
        })
      })
    }
  }
}
</script>
