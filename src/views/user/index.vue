<template>
  <div v-loading="tableLoading" class="app-container">
    <div class="search-part">
      <div class="search-item">
        <span class="search-label">名字：</span>
        <el-input v-model="searchData.name" size="mini" />
      </div>
      <div class="search-item">
        <span class="search-label">手机号：</span>
        <el-input v-model="searchData.phone" size="mini" />
      </div>
      <div class="search-item">
        <span class="search-label">邮箱：</span>
        <el-input v-model="searchData.email" size="mini" />
      </div>
      <div class="search-item">
        <span class="search-label">地址：</span>
        <el-input v-model="searchData.address" size="mini" />
      </div>
      <el-button
        size="mini"
        icon="el-icon-search"
        type="primary"
        @click="toSeachTableData"
      >查询</el-button>
      <el-button
        size="mini"
        icon="el-icon-delete"
        type="danger"
        @click="resetSearch"
      >重置</el-button>
    </div>
    <el-button
      type="primary"
      size="mini"
      style="margin-bottom: 10px"
      @click="addUser"
    >添加</el-button>
    <el-table
      :data="tableData"
      border
      :height="tableHeight"
      :header-cell-style="{
        background: '#FFFFFF',
        height: '40px',
        color: '#333333',
      }"
    >
      <el-table-column label="名字" prop="name" min-width="100px" />
      <el-table-column label="手机号" prop="phone" />
      <el-table-column label="邮箱" prop="email" />
      <el-table-column label="地址" prop="address" />
      <el-table-column label="操作" width="120px">
        <template slot-scope="scope">
          <el-button
            type="text"
            @click="editUser(scope.row)"
          >编辑</el-button>
          <el-button
            type="text"
            @click="deleteUser(scope.row)"
          >删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <Pagination :total="total" :page.sync="listQuery.pageNum" :limit.sync="listQuery.pageSize" @pagination="toSeachTableData" />
    <userEdit :visible.sync="dialogVisible" :row-info="currentRow" :type="dialogType" @refetchTable="toSeachTableData" />
  </div>
</template>

<script>
import { fetchUserList, delUser } from '@/api/user.js'
import Pagination from '@/components/Pagination/index.vue'
import userEdit from './components/userEdit.vue'
export default {
  components: {
    Pagination,
    userEdit
  },
  data() {
    return {
      tableLoading: false,
      dialogVisible: false,
      dialogType: 'add',
      currentRow: {},
      total: 0,
      tableHeight: 300,
      listQuery: {
        pageNum: 1,
        pageSize: 20
      },
      searchData: {
        name: '',
        phone: '',
        email: '',
        address: ''
      },
      tableData: [
        {
          name: '名字',
          phone: '手机号',
          email: '邮箱',
          address: '地址'
        }
      ]
    }
  },
  created() {
    this.toSeachTableData()
  },
  methods: {
    resetSearch() {
      this.searchData = {
        name: '',
        phone: '',
        email: '',
        address: ''
      }
      this.toSeachTableData()
    },
    toSeachTableData() {
      this.tableLoading = true
      const params = { ...this.listQuery, ...this.searchData }
      fetchUserList(params).then(res => {
        if (res.code !== 200) return
        this.tableData = res.data
        this.total = res.total
      }).finally(() => {
        this.tableLoading = false
        this.setTableHeight()
      })
    },
    addUser() {
      this.dialogVisible = true
      this.dialogType = 'add'
    },
    editUser(row) {
      this.dialogVisible = true
      this.dialogType = 'edit'
      this.currentRow = JSON.parse(JSON.stringify(row))
    },
    deleteUser(row) {
      this.$confirm('此操作将永久删除该用户, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        delUser({ id: row.id }).then(res => {
          if (res.code !== 200) return
          this.$message({
            type: 'success',
            message: '删除成功!'
          })
          this.toSeachTableData()
        })
      })
    },
    setTableHeight() {
      this.tableHeight = document.querySelector('#app').clientHeight - document.querySelector('.search-part').clientHeight - 195
    }
  }
}
</script>

<style lang="scss" scoped>
.search-part {
  display: flex;
  column-gap: 30px;
  margin-bottom: 20px;
}
.search-item {
  display: flex;
  align-items: center;
  .search-label {
    white-space: nowrap;
  }
}
.el-table {
  ::v-deep th {
    padding: 0;
  }
  ::v-deep td {
    padding: 0;
  }
}
</style>
