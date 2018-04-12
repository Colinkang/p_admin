<template>
<div>
  <div class="mianbao">
    活动管理/编辑活动
  </div>
  <el-row class="row-box">
    <el-col :span="2">
      活动大类
    </el-col>
    <el-col :span="20">
      <el-select v-model="activity.first_type" clearable placeholder="请选择" @change="secondTypeSelect()"  class="select-row">
        <el-option  v-for="item in activityFirstType" :key="item.value" :label="item.label" :value="item.value">
        </el-option>
      </el-select>

    </el-col>
  </el-row>
    <el-row class="row-box">
    <el-col :span="2">
      活动小类
    </el-col>
    <el-col :span="20">
      <el-select v-model="activity.second_type" clearable placeholder="请选择" class="select-row">
        <el-option v-for="item in activitySecondType" :key="item.value" :label="item.label" :value="item.value">
        </el-option>
      </el-select>

    </el-col>
  </el-row>
  <el-row class="row-box">
    <el-col :span="2">
      活动名称
    </el-col>
    <el-col :span="20">
      <el-input v-model="activity.title"></el-input>
    </el-col>
  </el-row>
  <el-row class="row-box">
    <el-col :span="2">
      封面图片
    </el-col>
    <el-col :span="1" class="img-upload-row">
 <el-upload
  class="upload-demo"
  :action="action"
  :on-success="handleChange"
  :file-list="fileList2"
  list-type="picture">
  <el-button size="small" type="primary">点击上传</el-button>
</el-upload>
    </el-col>
   
  </el-row>
  <el-row class="row-box">
    <el-col :span="2">
      活动信息
    </el-col>
    <el-col :span="14" :offset="5">
      <editor @article-change="articleChange" :content="activity.content" v-if="showEditor"></editor>
    </el-col>
  </el-row>
    <el-row class="row-box">
    <el-col :span="2">
      城市
    </el-col>
    <el-col :span="20">
      <el-input v-model="activity.city"></el-input>
    </el-col>
  </el-row>
  <el-row class="row-box">
    <el-col :span="2">
      活动地点
    </el-col>
    <el-col :span="20">
      <el-input v-model="activity.address"></el-input>
    </el-col>
  </el-row>

  <el-row class="row-box">
    <el-col :span="2">
      开始时间
    </el-col>
    <el-col :span="20">
      <el-date-picker
      class="select-row"
      v-model="activity.start_time"
      type="date"
      placeholder="选择日期">
    </el-date-picker>

    </el-col>
  </el-row>
  <el-row class="row-box">
    <el-col :span="2">
      结束时间
    </el-col>
    <el-col :span="20">
      <el-date-picker
      class="select-row"
      v-model="activity.end_time"
      type="date"
      placeholder="选择日期">
    </el-date-picker>

    </el-col>
  </el-row>
  <el-row style="margin-top:20px">
    <el-button type="primary" @click="add()">提交</el-button>
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
      fileList2: [],
      activity: {
        article_id: "",
        first_type: "",
        second_type: "",
        title: "",
        picture_path: "",
        content: "",
        city: "",
        address: "",
        start_time: "",
        end_time: ""
      },
      activityFirstType: [
        {
          value: "FM",
          label: "挚享FM"
        },
        {
          value: "OFFLINE",
          label: "线下活动"
        }
      ],
      activitySecondType: [
        {
          value: "CONTENT",
          label: "资讯详情"
        },
        {
          value: "ACTIVITY",
          label: "活动详情"
        },
        {
          value: "REVIEW",
          label: "活动回顾"
        }
      ]
    };
  },
  created() {
    var _this = this;
    let article_id = _this.$route.query.article_id;
    let result = post("/manager/activity/detail", { article_id: article_id });
    result.then(function(res) {
      if (res.data.code === "1001") {
        let row = res.data.data;
        console.log(1111, row);
        _this.activity.article_id = article_id;
        _this.activity.first_type = row.first_type;
        _this.activity.second_type = row.second_type;
        _this.activity.title = row.title;
        _this.activity.picture_path = row.picture_path;
        _this.activity.content = row.content;
        _this.activity.city = row.city;
        _this.activity.address = row.address;
        _this.activity.start_time = row.start_time;
        _this.activity.end_time = row.end_time;
        _this.showEditor = true;
        _this.fileList2.push({
          name: "test",
          url: _this.host + row.picture_path
        });
      }
    });
  },
  mounted() {
    //do something after mounting vue instance
    this.host = PORSCHE_HOST;
    this.action = this.host + "/fileupload";
    // console.log(2222, this.action);
  },
  methods: {
    articleChange(content) {
      // console.log(111, content);
      this.activity.content = this.escape2Html(content);
      console.log(2222,this.activity.content,6666);
    },
    escape2Html(str) {
      var arrEntities = {
        lt: "<",
        gt: ">",
        nbsp: " ",
        amp: "&",
        quot: '"'
      };
      return str.replace(/&(lt|gt|nbsp|amp|quot);/gi, function(all, t) {
        return arrEntities[t];
      });
    },
    secondTypeSelect() {
      var _this = this;
      _this.activity.second_type = "";
      if (_this.activity.first_type === "FM") {
        _this.activitySecondType = [
          {
            value: "CONTENT",
            label: "资讯详情"
          },
          {
            value: "ACTIVITY",
            label: "活动详情"
          },
          {
            value: "REVIEW",
            label: "活动回顾"
          }
        ];
      } else if (_this.activity.first_type === "OFFLINE") {
        _this.activitySecondType = [
          {
            value: "OFFLINE",
            label: "线下活动"
          }
        ];
      }
    },

    add() {
      let _this = this;
      // console.log(999, this.activity.content, 7777);
      // let content = this.activity.content.replace(
      //   /<style[\S\s]*?<\/style>/g,
      //   ""
      // );
      console.log(111, this.activity.content, 222);
      let result = post("/manager/activity/edit", {
        article_id: _this.activity.article_id,
        first_type: _this.activity.first_type,
        second_type: _this.activity.second_type,
        title: _this.activity.title,
        picture_path: _this.activity.picture_path,
        content: _this.activity.content,
        city: _this.activity.city,
        address: _this.activity.address,
        start_time: new Date(_this.activity.start_time).getTime().toString(),
        end_time: new Date(_this.activity.end_time).getTime().toString()
      });
      result.then(res => {
        if (res.data.code === "1001") {
          _this.$message({
            message: res.data.message,
            type: "success"
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
    },
    handleChange(response, file, fileList) {
      this.activity.picture_path = file.response.data.picture_path;
      this.fileList2 = fileList.slice(-1);
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
