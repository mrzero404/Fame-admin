<template>
  <div>
    <el-table :data="pageDatas" border style="width: 100%">
      <el-table-column prop="id" label="id" width="60"></el-table-column>
      <el-table-column prop="title" label="标题" min-width="150" show-overflow-tooltip></el-table-column>
      <el-table-column prop="publish" label="发布日期" min-width="150"
                       show-overflow-tooltip></el-table-column>
      <el-table-column prop="status" label="状态" width="100" show-overflow-tooltip></el-table-column>
      <el-table-column label="操作" width="150">
        <template slot-scope="scope">
          <el-button
            size="small"
            @click="handleEdit(scope.row.id)">编辑
          </el-button>
          <el-button
            size="small"
            type="danger"
            @click="handleDelete(scope.row.id)">删除
          </el-button>
        </template>
      </el-table-column>
    </el-table>
    <div class="admin-page">
      <el-pagination
        layout="total,prev, pager, next"
        @current-change="init"
        :current-page.sync="currentPage"
        :page-size="pageSize"
        :total="total">
      </el-pagination>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  export default {
    data: function () {
      return {
        pageDatas: [],
        total: 0,
        pageSize: 10,
        currentPage: 1
      }
    },
    methods: {
      handleEdit (id) {
        this.$router.push('/admin/page/publish/' + id)
      },
      handleDelete (id) {
        this.$confirm('此操作将永久删除该自定义页面, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'danger'
        }).then(() => {
          this.deletePage(id)
        }).catch(() => {
        })
      },
      initPageDatas (pages) {
        this.pageDatas = []
        for (let key in pages) {
          let data = pages[key]
          let page = {
            id: data.id,
            title: data.title,
            publish: this.$moment(data.created).format('YYYY-MM-DD HH:mm'),
            status: data.status
          }
          this.pageDatas.push(page)
        }
      },
      deletePage (id) {
        this.$api.auth.deletePage(id).then(data => {
          if (data.success) {
            this.$message({
              type: 'success',
              message: '删除成功!'
            })
            this.init(this.$route.query.page)
          } else {
            this.$message({
              type: 'error',
              message: '删除失败!'
            })
          }
        })
      },
      init (page) {
        this.$api.auth.getPages(page || 1).then(data => {
          if (data.success) {
            this.initPageDatas(data.data.list)
            this.total = data.data.total
            this.pageSize = data.data.pageSize
            this.currentPage = Number(page) || 1
          } else {
            this.$message({
              message: '获取自定义页面列表失败,' + data.msg,
              type: 'error'
            })
          }
        })
      }
    },
    mounted () {
      this.init()
    }
  }
</script>

<style>
  .el-table ::-webkit-scrollbar {
    display: block;
    height: 10px;
  }

  .el-table ::-webkit-scrollbar-thumb {
    background-color: #324157;
  }

  .el-table ::-webkit-scrollbar-thumb:active {
    background-color: #00aff0
  }

  @media screen and (min-width: 600px) {
    .el-table ::-webkit-scrollbar {
      display: block;
      height: 10px;
    }
  }

  @media screen and (max-width: 600px) {
    .el-table ::-webkit-scrollbar {
      display: none;
    }
  }
</style>

<style scoped>
  .el-table {
    border: 1px solid #e6ebf5;
  }

  .admin-page {
    margin-top: 30px;
    text-align: center;
  }
</style>
