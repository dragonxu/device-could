<template>
  <div class="turnaround-plans">
    <div class="userCase">
      <div class="top">
        <el-button
          size="small"
           @click="outerVisible = true"
        >审核</el-button>
        <el-button
          size="small"
          @click="revoke"
        >故障消除</el-button>
        <el-button size="small">删除</el-button>
        <div class="search">
          <el-input
            type="search"
            placeholder="如故障编码，设备名称，位号，描述"
            size="small"
            v-model="faultKey"
          ></el-input>
          <el-button
            size="small"
            @click="search"
          >搜索</el-button>
          <span style="padding-left:10px;">高级搜索</span>
        </div>
      </div>
      <div class="bottom">
        <div>
          <v-table
            :select-all="selectALL"
            :select-group-change="selectGroupChange"
            is-horizontal-resize
            column-width-drag
            :multiple-sort="false"
            style="width:100%;min-height:400px;"
            :columns="columns"
            :table-data="tableData"
            row-hover-color="#eee"
            row-click-color="#edf7ff"
            :row-dblclick="toDetails"
          ></v-table>
          <div
            class="mt20 mb20 bold"
            style="text-align:center;margin-top:30px;"
          >
            <v-pagination
              @page-change="pageChange"
              @page-size-change="pageSizeChange"
              :total="1"
              :page-size="pageSize"
              :layout="['total', 'prev', 'pager', 'next', 'sizer', 'jumper']"
            ></v-pagination>
          </div>
        </div>
      </div>
    </div>
    <el-dialog title="审核" :visible.sync="outerVisible">
      <audit></audit>
    <el-dialog
      title="人员添加"
      :visible.sync="innerVisible"
      append-to-body>
      <personnel></personnel>
    </el-dialog>
    <div slot="footer" class="dialog-footer">
      <el-button @click="outerVisible = false" size="mini">取 消</el-button>
      <el-button type="primary" size="mini">提交</el-button>
      <el-button type="primary" @click="innerVisible = true" size="mini">添加下一级审批人</el-button>
    </div>
  </el-dialog>
  </div>
</template>
<script>
import audit from "./breakdown/Audit";
import personnel from "./breakdown/Personnel";
export default {
  data() {
    return {
      outerVisible: false,
      innerVisible: false,
      faultId: "",
      faultKey: "",
      auditShow: false,
      auditdetails:"",
      pageIndex: 1,
      pageSize: 10,
      tableData: [
        {
          faultNo:"2222",
          state:"2222"
        }
      ],
      tableDate: [],
      columns: [
        {
          width: 50,
          titleAlign: "center",
          columnAlign: "center",
          type: "selection"
        },
        {
          field: "faultNo",
          title: "故障编号",
          width: 80,
          titleAlign: "center",
          columnAlign: "center",
          isResize: true
          //   orderBy: ""
        },
        {
          field: "state",
          title: "状态",
          width: 80,
          titleAlign: "center",
          columnAlign: "center",
          isResize: true
        },
        {
          field: "deviceName",
          title: "设备名称",
          width: 80,
          titleAlign: "center",
          columnAlign: "center",
          isResize: true
        },
        {
          field: "deviceSpec",
          title: "规格/型号",
          width: 100,
          titleAlign: "center",
          columnAlign: "center",
          isResize: true
        },
        {
          field: "faultLevel",
          title: "故障等级",
          width: 100,
          titleAlign: "center",
          columnAlign: "center",
          isResize: true
        },
        {
          field: "faultSource",
          title: "故障来源",
          width: 100,
          titleAlign: "center",
          columnAlign: "center",
          isResize: true
        },
        {
          field: "faultDesc",
          title: "故障描述",
          width: 80,
          titleAlign: "center",
          columnAlign: "center",
          isResize: true
        },
        {
          field: "causeAnalysis",
          title: "原因分析",
          width: 100,
          titleAlign: "center",
          columnAlign: "center",
          isResize: true
        }
      ]
    };
  },
  methods: {
    toDetails(rowIndex, rowData, column){
      this.$router.push({path:"/BreakDetails"})
    },
    auditHide(params){
      this.auditShow=params;
    },
    toAudit() {
      this.auditShow = true;
    },
    selectGroupChange(selection) {
      console.log("select-group-change", selection);
      this.auditdetails=selection
    },
    selectALL(selection) {
      console.log("select-aLL", selection);
    },
    selectChange(selection, rowData) {
      console.log("select-change", selection, rowData);
    },
    getTableData() {
      this.tableData = this.tableDate.slice(
        (this.pageIndex - 1) * this.pageSize,
        this.pageIndex * this.pageSize
      );
    },
    pageChange(pageIndex) {
      this.pageIndex = pageIndex;
      this.getTableData();
      console.log(pageIndex);
    },
    pageSizeChange(pageSize) {
      this.pageIndex = 1;
      this.pageSize = pageSize;
      this.getTableData();
    },
    sortChange(params) {
      if (params.height.length > 0) {
        this.tableConfig.tableData.sort(function(a, b) {
          if (params.height === "asc") {
            return a.height - b.height;
          } else if (params.height === "desc") {
            return b.height - a.height;
          } else {
            return 0;
          }
        });
      }
    },

    load() {
      this.axios
        .get(this.global.apiSrc + "/fault/list", { params: { page: -1 } })
        .then(response => {
          this.tableData = response.data.data;
          this.springReplacement();
          this.tableDate = this.tableData;
        })
        .catch(function(error) {
          console.log(error);
        });
    },
    springReplacement() {
      for (let i = 0; i < this.tableData.length; i++) {
        if (this.tableData[i].state === 0) {
          this.tableData[i].state = "待审核";
        }
        if (this.tableData[i].state === 1) {
          this.tableData[i].state = "审核通过";
        }
        if (this.tableData[i].state === 2) {
          this.tableData[i].state = "禁用";
        }
        if (this.tableData[i].state === 3) {
          this.tableData[i].state = "已删除";
        }
        if (this.tableData[i].state === 4) {
          this.tableData[i].state = "审核中";
        }
        if (this.tableData[i].state === 5) {
          this.tableData[i].state = "待处理";
        }
        if (this.tableData[i].state === 6) {
          this.tableData[i].state = "已消除";
        }
        if (this.tableData[i].state === 7) {
          this.tableData[i].state = "已撤销";
        }
        if (this.tableData[i].state === 10) {
          this.tableData[i].state = "审批被驳回";
        }
        if (this.tableData[i].faultLevel === 1) {
          this.tableData[i].faultLevel = "低";
        }
        if (this.tableData[i].faultLevel === 2) {
          this.tableData[i].faultLevel = "中";
        }
        if (this.tableData[i].faultLevel === 3) {
          this.tableData[i].faultLevel = "高";
        }
        if (this.tableData[i].faultSource === "0") {
          this.tableData[i].faultSource = "人工提交";
        }
        if (this.tableData[i].faultSource === "1") {
          this.tableData[i].faultSource = "设备采集";
        }
      }
    },
    revoke() {
      let qs = require("qs");
      let data = qs.stringify({
        faultId: this.faultId
      });
      this.axios
        .post(this.global.apiSrc + "/fault/revoke", data)
        .then(response => {
          console.log(response);
        })
        .catch(function(error) {
          console.log(error);
        });
    },
    search() {
      this.axios
        .get(this.global.apiSrc + "/fault/search", {
          params: { keyword: this.faultKey, page: -1 }
        })
        .then(response => {
          this.tableData = response.data.data;
          this.springReplacement();
          this.tableDate = this.tableData;
          // console.log(response)
        })
        .catch(function(error) {
          console.log(error);
        });
    }
  },
  created() {
    this.load();
  },
  components: {
    audit,
    personnel
  }
};
</script>

<style lang="less" scoped>
@blue: #409eff;
@Success: #67c23a;
@Warning: #e6a23c;
@Danger: #f56c6c;
@Info: #dde2eb;
@border: 1px solid #dde2eb;
.turnaround-plans {
  // padding-left: 220px;
  .userCase {
    width: 100%;
    padding: 10px;
    .top {
      height: 60px;
      line-height: 60px;
      border: 1px solid @Info;
      border-radius: 5px;
      padding-left: 10px;
      .search {
        float: right;
        width: 40%;
        font-size: 12px;
        .el-input {
          width: 60%;
        }
        span {
          cursor: pointer;
          &:hover {
            color: @blue;
          }
        }
      }
    }
    .bottom {
      padding: 10px;
      font-size: 12px;
      border: 1px solid @Info;
      margin-top: 10px;
      min-height: 500px;
      border-radius: 5px;
    }
  }
}
</style>
