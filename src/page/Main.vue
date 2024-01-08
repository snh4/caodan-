<template>
  <div class="contain">
    <el-form :inline="true" :model="filter" class="demo-form-inline">
      <el-form-item label="审核人：">
        <el-input v-model="filter.auditor" placeholder="请输入审核人"></el-input>
      </el-form-item>
      <el-form-item label="审核时间：">
        <el-date-picker v-model="filter.auditTime" format="YYYY-MM-DD"
                        value-format="YYYY-MM-DD" type="date" placeholder="选择日期"></el-date-picker>
      </el-form-item>
      <el-form-item label="状态：">
        <el-select v-model="filter.state" placeholder="选择状态">
          <el-option label="已审核" value="已审核"></el-option>
          <el-option label="未审核" value="未审核"></el-option>
        </el-select>
      </el-form-item>
      
      <el-form-item label="境内发货人：">
        <el-input v-model="filter.sender" placeholder="请输入境内发货人"></el-input>
      </el-form-item>
      <el-form-item label="境外收货人：">
        <el-input v-model="filter.receiver" placeholder="请输入境外收货人"></el-input>
      </el-form-item>

      <el-form-item>
        <el-button @click="search" type="primary">查询</el-button>
      </el-form-item>
    </el-form>

    <div class="button-group">
      <el-button @click="addNewItem" type="primary">新建</el-button>
      <!-- <el-button @click="editItem" type="success">编辑</el-button> -->
      <!--<el-button @click="deleteItem" type="danger">删除</el-button>-->
    </div>
    
    
    <el-table :data="pagedData" @row-click="handleRowClick" highlight-current-row>

      <!-- <el-table-column prop="ProcessingRecord" label="序号" sortable></el-table-column> -->
      <el-table-column label="序号">
        <template #default="{ $index }">
          <span>{{ $index + 1  }}</span>
        </template>
      </el-table-column>
      <el-table-column prop="ManualReviewPersonnel" label="审核人" sortable></el-table-column>
      <el-table-column prop="CompanyName" label="公司名称" sortable></el-table-column>
      <!-- <el-table-column prop="number" label="预录入编号" sortable></el-table-column> -->

      <el-table-column prop="DataEntryTime" label="数据录入时间" sortable></el-table-column>
      <el-table-column prop="ManualReviewTime" label="人工审核时间" sortable></el-table-column>
      <el-table-column prop="ReviewTime" label="机器复核时间" sortable></el-table-column>
      <el-table-column prop="ManualReviewStatus" label="审核状态" sortable></el-table-column>
      
      <!-- <el-table-column prop="DragName" label="草单名称" sortable></el-table-column> -->
      <el-table-column prop="DomesticShipper" label="国内托运人" sortable></el-table-column>
      <el-table-column prop="OverseasConsignee" label="海外收货人" sortable></el-table-column>
      <el-table-column label="操作">
        <template #default="{ row,$index }">
          <el-button @click="editItem(row)" type="success">编辑</el-button>
          <el-button @click="deleteItem(row,$index)" type="danger">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="currentPage"
      :page-sizes="[15, 30, 50, 100]"
      :page-size="pageSize"
      background
      layout="total, sizes, prev, pager, next, jumper"
      :total="filteredData.length"
    ></el-pagination>
    
  </div>
</template>
<script>
import api from "./login/api.js";

export default {
  created() {
    // 页面创建时执行一次搜索，显示所有记录
    this.search();
  },
  data() {
    return {
      tableData: [
        { index: 1, number: '001', auditor: '张三', auditTime: '2022-01-01' ,state:'已审核'},
        { index: 2, number: '002', auditor: '李四', auditTime: '2022-01-02' ,state:'未审核'},
        { index: 3, number: '001', auditor: '张三', auditTime: '2022-01-01' ,state:'已审核'},
        { index: 4, number: '002', auditor: '李四', auditTime: '2022-01-02' ,state:'未审核'},
        { index: 5, number: '001', auditor: '张三', auditTime: '2022-01-01' ,state:'已审核'},
        { index: 6, number: '002', auditor: '李四', auditTime: '2022-01-02' ,state:'未审核'},
        { index: 7, number: '001', auditor: '张三', auditTime: '2022-01-01' ,state:'已审核'},
        { index: 8, number: '002', auditor: '李四', auditTime: '2022-01-02' ,state:'未审核'},
        { index: 9, number: '001', auditor: '张三', auditTime: '2022-01-01' ,state:'已审核'},
        { index: 10, number: '002', auditor: '李四', auditTime: '2022-01-02' ,state:'未审核'},
        { index: 11, number: '001', auditor: '张三', auditTime: '2022-01-01' ,state:'已审核'},
        { index: 12, number: '002', auditor: '李四', auditTime: '2022-01-02' ,state:'未审核'},
        { index: 13, number: '001', auditor: '张三', auditTime: '2022-01-01' ,state:'已审核'},
        { index: 14, number: '002', auditor: '李四', auditTime: '2022-01-02' ,state:'未审核'},
        { index: 15, number: '001', auditor: '张三', auditTime: '2022-01-01' ,state:'已审核'},
        { index: 16, number: '002', auditor: '李四', auditTime: '2022-01-02' ,state:'未审核'},
        { index: 17, number: '001', auditor: '张三', auditTime: '2022-01-01' ,state:'已审核'},
        { index: 18, number: '002', auditor: '李四', auditTime: '2022-01-02' ,state:'未审核'},
        { index: 19, number: '001', auditor: '张三', auditTime: '2022-01-01' ,state:'已审核'},
        { index: 20, number: '002', auditor: '李四', auditTime: '2022-01-02' ,state:'未审核'},
        { index: 21, number: '001', auditor: '张三', auditTime: '2022-01-01' ,state:'已审核'},
        { index: 22, number: '002', auditor: '李四', auditTime: '2022-01-02' ,state:'未审核'},
        { index: 23, number: '001', auditor: '张三', auditTime: '2022-01-01' ,state:'已审核'},
        { index: 24, number: '002', auditor: '李四', auditTime: '2022-01-02' ,state:'未审核'},
        { index: 25, number: '001', auditor: '张三', auditTime: '2022-01-01' ,state:'已审核'},
        { index: 26, number: '002', auditor: '李四', auditTime: '2022-01-02' ,state:'未审核'},
        { index: 27, number: '001', auditor: '张三', auditTime: '2022-01-01' ,state:'已审核'},
        { index: 28, number: '002', auditor: '李四', auditTime: '2022-01-02' ,state:'未审核'},
        { index: 29, number: '001', auditor: '张三', auditTime: '2022-01-01' ,state:'已审核'},
        { index: 30, number: '002', auditor: '李四', auditTime: '2022-01-02' ,state:'未审核'},
        { index: 31, number: '001', auditor: '张三', auditTime: '2022-01-01' ,state:'已审核'},
        { index: 32, number: '002', auditor: '李四', auditTime: '2022-01-02' ,state:'未审核'},
        { index: 33, number: '001', auditor: '张三', auditTime: '2022-01-01' ,state:'已审核'},
        { index: 34, number: '002', auditor: '李四', auditTime: '2022-01-02' ,state:'未审核'},
        { index: 35, number: '001', auditor: '张三', auditTime: '2022-01-01' ,state:'已审核'},
        { index: 36, number: '002', auditor: '李四', auditTime: '2022-01-02' ,state:'未审核'},
      ],
      filteredData: [],
      selectedRow: null,
      filter: {
        auditor: '',
        auditTime: '',
        state: '',
        sender:'',
        receiver:'',
      },
      currentPage:1,
      pageSize:15,
    };
  },
  computed: {
    pagedData() {
      const start = (this.currentPage - 1) * this.pageSize;
      const end = start + this.pageSize;
      return this.filteredData.slice(start, end);
    },
  },
  methods: {
    
    addNewItem() {
      // 新建事件处理逻辑
      console.log('新建');
      // this.$router.push({ path: '/detail' })
      this.$router.push({ name: 'Detail', params: { mode: 'new' } });

    },
    editItem(row) {
    
      const selectedRowId = row.ProcessingRecord;
      // const selectedRowId = this.selectedRow.ProcessingRecord;
      console.log(selectedRowId);
      const detailApiUrl = `/api/detail/${selectedRowId}`;
      // Navigate to the edit page and pass the ID as a parameter
      this.$router.push({ name: 'Detail', params: { mode: 'edit', id: selectedRowId } });
    },
    async deleteItem(row,index) {
      // 删除事件处理逻辑
      const selectedRowId = row.ProcessingRecord;
      console.log(index)
      // 弹出确认对话框
      const userConfirmed = window.confirm(`确定要删除记录: ${index+1} 吗？`);

      if (userConfirmed) {
          try {
              const response = await api.delete(`/processlist/${selectedRowId}`);

              if (response.status === 200) {
                  console.log('记录删除成功');
                  // 删除成功后重新搜索，刷新记录列表
                  this.search();
              } else {
                  console.error('删除失败');
              }
          } catch (error) {
              console.error('发生错误', error);
          }
      } else {
          console.log('用户取消删除操作');
      }
    },

    handleRowClick(row) {
      this.selectedRow = row;
    },
    async search() {
      const token = sessionStorage.getItem('token');
      const queryParams = {
        manual_review_status: this.filter.state,
        manual_review_time: this.filter.auditTime,
        manual_review_personnel: this.filter.auditor,
        domestic_shipper: this.filter.sender,
        overseas_consignee: this.filter.receiver,
    };
    console.log(queryParams)
      // const response = await api.get('/processlist/');
      const response = await api.get('/processlist/', { params: queryParams });

    // const response = await api.get('/processlist/', {
    //   headers: {
    //     'Authorization': `${token}`,
    //   },
    //   // 其他请求参数
    // });
      // this.tableData = response.data;
      this.filteredData = response.data;
    // 处理响应
    console.log(response.data);
    // console.log(response.data)
      // 查询事件处理逻辑
      // 根据筛选条件filter进行数据查询
    //   this.filteredData = this.tableData.filter(item => {
    //     const isAuditTimeMatched = !this.filter.auditTime || item.auditTime.includes(this.filter.auditTime);
    //     console.log(typeof this.filter.auditTime);
    //     console.log(typeof this.filter.auditor);
    //     console.log(typeof this.filter.state);
    //     console.log( this.filter.auditTime);
    //     console.log( this.filter.auditor);
    //     console.log( this.filter.auditor);
    //     return (
    //       item.auditor.includes(this.filter.auditor) &&
    //       isAuditTimeMatched &&
    //       item.state.includes(this.filter.state)
    // );
    //   });
    },
    handleSizeChange(val) {
      this.pageSize = val;
      this.currentPage = 1;
    },
    handleCurrentChange(val) {
      this.currentPage = val;
    },
    
  },
};
</script>
<style>
.contain {
  height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.button-group {
  margin-bottom: 20px;
}
</style>
