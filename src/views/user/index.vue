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
    >添加</el-button>
    <el-table
      :data="tableData"
      border
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
          >编辑</el-button>
          <el-button
            type="text"
          >删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <Pagination :total="total" :page.sync="listQuery.pageNum" :limit.sync="listQuery.pageSize" @pagination="toSeachTableData" />
  </div>
</template>

<script>
import { fetchUserList } from '@/api/user.js'
import Pagination from '@/components/Pagination/index.vue'
export default {
  components: {
    Pagination
  },
  data() {
    return {
      tableLoading: false,
      total: 0,
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
      })
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
