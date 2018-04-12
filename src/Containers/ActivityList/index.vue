<template>
<div>
  <div class="mianbao">
    活动管理/活动列表
  </div>
  <el-row class="register-content-header">
    <el-col :span="2">活动大类</el-col>
    <el-col :span="2">活动小类</el-col>
    <el-col :span="2">标题</el-col>
    <el-col :span="2">活动时间</el-col>
    <el-col :span="1">城市</el-col>
    <el-col :span="1">地址</el-col>
    <el-col :span="1">参加数</el-col>
    <el-col :span="1">浏览数</el-col>
    <el-col :span="2">感兴趣数</el-col>
    <el-col :span="2">创建时间</el-col>
    <el-col :span="8">操作 </el-col>
  </el-row>
  <el-row class="register-content-row" v-for="(item,index) in tableData" :key="item.id">
    <el-col :span="2">{{item.first_type?item.first_type:'-'}}</el-col>
    <el-col :span="2">{{item.second_type?item.second_type:'-'}}</el-col>
    <el-col :span="2">{{item.title?item.title:'-'}}</el-col>
    <el-col :span="2">{{item.start_time+' 至 '+item.end_time}}</el-col>
    <el-col :span="1">{{item.city?item.city:'-'}}</el-col>
    <el-col :span="1">{{item.address?item.address:'-'}}</el-col>
    <el-col :span="1">{{item.join_count}}</el-col>
    <el-col :span="1">{{item.visit_count}}</el-col>
    <el-col :span="2">{{item.upvote_count}}</el-col>
    <el-col :span="2">{{item.created_time?item.created_time:'-'}}</el-col>
    <el-col :span="8">
      <el-button size="small" @click="activityUserList(index, item)">参加人员</el-button>
      <el-button size="small" @click="actvityCommentList(index, item)">查看评论</el-button>
      <el-button size="small" @click="activityEdit(index, item)">编辑</el-button>
      <el-button size="small" @click="activityDelete(index, item)">删除</el-button>
    </el-col>
  </el-row>

  <div class="block">
    <el-pagination @current-change="handleCurrentChange" :current-page.sync="currentPage" :page-size="count" layout="total, prev, pager, next" :total="total">
    </el-pagination>
  </div>
</div>
</template>

  <script>
import { post } from "../../common/post";
import { PORSCHE_HOST } from "../../common/host";
import { timestampToDate } from "../../common/utils";
export default {
  data() {
    return {
      offset: 0,
      count: 10,
      total: 100,
      currentPage: 1,
      tableData: [],
      host: ""
    };
  },
  mounted() {
    //do something after mounting vue instance
    this.host = PORSCHE_HOST;
  },
  created: function() {
    let _this = this;
    let result = post("/manager/activity/list", {
      offset: _this.offset,
      count: _this.count
    });
    result.then(res => {
      let rows = res.data.data.rows;
      for (let i = 0; i < rows.length; i++) {
        rows[i].created_time = timestampToDate(rows[i].created_time);
        if (rows[i].start_time)
          rows[i].start_time = timestampToDate(rows[i].start_time);
        else rows[i].start_time = "-";
        if (rows[i].end_time)
          rows[i].end_time = timestampToDate(rows[i].end_time);
        else rows[i].end_time = "-";
        if (rows[i].first_type === "FM") {
          rows[i].first_type = "挚享FM";
          if ((rows[i].second_type = "CONTENT")) {
            rows[i].second_type = "文章详情";
          } else if ((rows[i].second_type = "REVIEW")) {
            rows[i].second_type = "活动回顾";
          } else if ((rows[i].second_type = "ACTIVITY")) {
            rows[i].second_type = "活动参与";
          }
        } else if (rows[i].first_type === "OFFLINE") {
          rows[i].first_type = "线下活动";
          rows[i].second_type = "线下活动";
        }
        if (rows[i].title.length > 12)
          rows[i].title = rows[i].title.slice(0, 12) + "...";
      }
      _this.tableData = rows;
      _this.total = res.data.data.count;
    });
  },
  methods: {
    handleCurrentChange(val) {
      var _this = this;
      _this.offset = (parseInt(val) - 1) * _this.count;
      console.log(`当前页: ${val}`);
      let result = post("/manager/activity/list", {
        offset: _this.offset,
        count: _this.count
      });
      result.then(res => {
        let rows = res.data.data.rows;
        for (let i = 0; i < rows.length; i++) {
          rows[i].created_time = timestampToDate(rows[i].created_time);
          if (rows[i].start_time)
            rows[i].start_time = timestampToDate(rows[i].start_time);
          else rows[i].start_time = "-";
          if (rows[i].end_time)
            rows[i].end_time = timestampToDate(rows[i].end_time);
          else rows[i].end_time = "-";
          if (rows[i].first_type === "FM") {
            rows[i].first_type = "挚享FM";
            if ((rows[i].second_type = "CONTENT")) {
              rows[i].second_type = "文章详情";
            } else if ((rows[i].second_type = "REVIEW")) {
              rows[i].second_type = "活动回顾";
            } else if ((rows[i].second_type = "ACTIVITY")) {
              rows[i].second_type = "活动参与";
            }
          } else if (rows[i].first_type === "OFFLINE") {
            rows[i].first_type = "线下活动";
            rows[i].second_type = "线下活动";
          }
          if (rows[i].title.length > 12)
            rows[i].title = rows[i].title.slice(0, 12) + " ...";
        }
        _this.tableData = rows;
        _this.total = res.data.data.count;
      });
    },
    activityUserList(index, row) {
      console.log(index, 111, row);
      this.$router.push({
        path: "/activity/user/list",
        query: {
          article_id: row.uuid
        }
      });
    },
    actvityCommentList(index, row) {
      this.$router.push({
        path: "/activity/comment/list",
        query: {
          article_id: row.uuid
        }
      });
    },
    activityEdit(index, row) {
      this.$router.push({
        path: "/activity/edit",
        query: {
          article_id: row.uuid
        }
      });
    },
    activityDelete(index, row) {
      console.log(2222, row);
      var _this = this;
      _this
        .$confirm("此操作将永久删除该文件, 是否继续?", "提示", {
          confirmButtonText: "确定",
          cancelButtonText: "取消",
          type: "warning"
        })
        .then(() => {
          let result = post("/manager/activity/delete", {
            article_id: row.uuid
          });
          result.then(function(res) {
            if (res.data.code === "1001") {
              _this.$message({
                type: "success",
                message: "删除成功!"
              });
              _this.handleCurrentChange(1);
            }
          });
        })
        .catch(() => {
          _this.$message({
            type: "info",
            message: "已取消删除"
          });
        });
    }
  }
};
</script>

<style scoped="">
.register-content-row {
  height: 100px;
  border-bottom: 1px solid #ccc;
  font-size: 14px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.register-content-header {
  border-bottom: 1px solid #ccc;
  background-color: #eee;
  margin-top: 20px;
  height: 40px;
  line-height: 40px;
  font-weight: bold;
}

.input-container .el-col {
  padding-left: 10px;
  padding-right: 10px;
  margin-top: 20px;
}

.block {
  margin-top: 20px;
}

.avatar {
  width: 60px;
  height: 60px;
  margin-left: 20px;
}
.avatar img {
  width: 100%;
  height: 100%;
  border-radius: 5px;
  overflow: hidden;
  object-fit: cover;
}
</style>
