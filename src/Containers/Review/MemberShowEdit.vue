<template>
<div>
  <div class="mianbao">
    审核管理/挚享者说内容
  </div>
    <el-row class="row-box">
    <el-col :span="3">
     昵称
    </el-col>
    <el-col :span="20">
      <el-input v-model="nickname" disabled></el-input>
    </el-col>
  </el-row>
  <el-row class="row-box">
    <el-col :span="3">
      挚享者说标题
    </el-col>
    <el-col :span="20">
      <el-input v-model="title"></el-input>
    </el-col>
  </el-row>
  <el-row class="row-box">
    <el-col :span="3">
      挚享者说详情
    </el-col>
     <el-col :span="20">
      <div v-for="list in content" :key="list.id" style="text-align: left;">
        <textarea name="list.id" v-model="list.content"  id="list.id" style="width:100%;height:100px" cols="100" rows="20"></textarea>
        <img width="50%"  height="50%" :src="host+list.imgpic" alt="">
    </div>
    </el-col>
    <!-- <el-col :span="14" v-for="list in content">
     <el-input v-model="list.content"></el-input>
    </el-col> -->
  </el-row>
     <el-row class="row-box">
    <el-col :span="3">
      挚享者说状态
    </el-col>
    <el-col :span="6">
      <el-select v-model="status" clearable placeholder="状态" style="width:100%">
        <el-option v-for="item in status_array" :key="item.value" :label="item.label" :value="item.value">
        </el-option>
      </el-select>
    </el-col>
  </el-row>
  <el-row style="margin-top:20px">
    <el-button type="primary" @click="update()">提交</el-button>
  </el-row>
</div>
</template>
<script>
import { post } from "../../common/post";
import { PORSCHE_HOST } from "../../common/host";
import Editor from "../../components/Editor/editor.vue";
export default {
  components: {
    Editor
  },
  data() {
    return {
      showEditor: false,
      action: "",
      host: "",
      nickname: "",
      article_id: "",
      title: "",
      content: "",
      status: "",
      status_array: [
        {
          value: "ENABLED",
          label: "通过"
        },
        {
          value: "REVIEW",
          label: "审核中"
        }
      ]
    };
  },
  created() {
    var _this = this;
    _this.article_id = _this.$route.query.article_id;
    let result = post("/manager/review/show/detail", {
      article_id: _this.article_id
    });
    result.then(function(res) {
      let row = res.data.data;
      _this.title = row.title;
      _this.content = JSON.parse(row.content);
      _this.status = row.status;
      _this.nickname = row.nickname;
    });
  },
  mounted() {
    //do something after mounting vue instance
    this.host = PORSCHE_HOST;
    this.action = this.host + "/fileupload";
  },
  methods: {
    update() {
      let _this = this;
      console.log(111,_this.content);
      let result = post("/manager/review/show/edit", {
        article_id: _this.article_id,
        title: _this.title,
        content: JSON.stringify(_this.content),
        status: _this.status
      });
      result.then(res => {
        if (res.data.code === "1001") {
          _this.$message({
            message: res.data.message,
            type: "success"
          });
          _this.$router.push({
            path: "/review/show/list"
          });
        } else if (res.data.code === "1003") {
          _this.$message({
            message: res.data.message,
            type: "warning"
          });
        } else {
          this.$message.error(res.data.message);
        }
      });
    }
  }
};
</script>
<style scoped>
.row-box {
  margin-top: 10px;
}

.select-row {
  width: 100%;
}
</style>
