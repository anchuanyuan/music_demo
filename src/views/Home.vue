<template>
  <div class="home">
    <div class="main_box">
      <div class="search">
        <el-select
          v-model="sourceValue"
          placeholder="选择源"
          :default-first-option="true"
          style="width:100px "
        >
          <el-option
            v-for="(item, index) in source"
            :key="index"
            :value="item"
            :label="item"
          >
          </el-option>
        </el-select>
        <el-input v-model="keyword"></el-input
        ><el-button
          icon="el-icon-search"
          circle
          @click="search"
          placeholder="请输入要查询的关键词"
        ></el-button>
      </div>
      <div class="list">
        <el-button-group style="float:left;margin-left:4vw">
          <el-button icon="el-icon-arrow-left">上一页</el-button>
          <el-button
            >下一页<i class="el-icon-arrow-right el-icon--right"></i
          ></el-button>
          <audio controls ref="audio">
            >
          </audio>
        </el-button-group>
        <el-table :data="songList" style="width: 90% ;margin:0 auto">
          <el-table-column prop="SongName" label="歌曲名称" min-width="180">
          </el-table-column>
          <el-table-column prop="SingerName" label="歌手" min-width="180">
          </el-table-column>
          <el-table-column label="试听">
            <template slot-scope="scope">
              <!-- <template v-slot:music="scope"> -->
              <div>
                <i
                  class="el-icon-video-play"
                  @click="getMusic(scope.row.musicID)"
                ></i>
              </div>
            </template>
          </el-table-column>
        </el-table>
      </div>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
// import $ from "jquery"
export default {
  name: "Home",
  components: {},
  data() {
    return {
      list: [],
      musicAddress: "", // 播放地址
      songList: [], // 歌曲列表
      keyword: "",
      source: ["kugou", "qq", "netease", "kuwo", "xiami"],
      sourceValue: "",
      pageData: {
        pageNo: 1,
        pageSize: 5,
        totalNubmer: 0
      }
    }
  },
  methods: {
    search() {
      if (!this.keyword) return this.$message.error("请先输入关键词哦")
      if (!this.sourceValue) return this.$message.error("选个源吧")
      this.$http
        .get("https://www.xiaoa.xin/musicdown/music/api.php", {
          params: {
            keyword: this.keyword,
            filter: "list",
            from: this.sourceValue,
            page: this.pageData.pageNo
          }
        })
        .then(res => {
          console.log(res.data)
          this.songList = res.data
          this.$message.success("查询成功")
        })
        .catch(err => {
          console.log(err)
          this.$message.error(err)
        })
      /*   $.ajax({
        url: "http://www.xiaoa.xin/musicdown/music/api.php",
        type: "GET",
        dataType: "jsonp", //指定服务器返回的数据类型
        data: {
          keyword: this.keyword,
          filter: "list",
          from: "kugou",
          page: this.pageData.pageNo
        },
        success: function(data) {
          var result = JSON.stringify(data) //json对象转成字符串
          console.log(result)
        }
      }) */
    },
    getMusic(musicID) {
      this.$http
        .get("https://www.xiaoa.xin/musicdown/music/api.php?", {
          params: {
            musicID,
            filter: "music",
            from: this.sourceValue
          }
        })
        .then(res => {
          console.log(res)
          this.$refs.audio.src = res.data[0].url
          this.$refs.audio.autoplay = true
          if (res.data[0].code == 403) {
            return this.$message.error(res.data[0].error)
          }
          console.log(this.$refs.audio)
        })
        .catch(err => {
          console.log(err)
        })
    }
  },
  mounted() {
    this.keyword = "陈小春"
    this.sourceValue = "kugou"
    this.search()
  }
}
</script>
<style lang="less" scoped>
.main_box {
  position: relative;
  padding-top: 10px;
  width: 70vw;
  height: 700px;
  margin: 100px auto 0;
  box-shadow: 10px 10px 5px #888888;
  border-radius: 50px;
  overflow: hidden;
  background: linear-gradient(
    25deg,
    rgb(231, 78, 86),
    rgb(227, 140, 88),
    rgb(217, 191, 87),
    rgb(198, 239, 85)
  );
}

.search {
  // width: 20vw;
  height: 50px;
  margin: 20px auto;
  // display: flex;
  // justify-content: center;
  .el-input {
    width: 200px;
    margin-right: 20px;
    border-radius: 25px;
  }
}
.el-table {
  border-radius: 15px;
}
audio {
  position: absolute;
  right: 57px;
  top: 79px;
}

// .search .el-input__inner {
//   width: 80%;
// }
</style>
