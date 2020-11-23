<template>
  <div class="" :class="{on: open}">
    <div class="mask" @click="close()"></div>
    <div class="modal_panel image_panel" id="image_panel_importance">
      <div class="modal_head">
        <span class="fl">视频管理</span>
        <a href="javascript:" @click="close()" class="close iconfont">&#xe616;</a>
      </div>
      <div class="modal_body">
        <div class="upload_box clearfix">
          <span></span>
          <div class="fr">
            <span v-if="!uploadList">相同视频上传两次，第二次不上传！</span>
            <span v-if="uploadList">视频大小不超过500M</span>
            <input type="file" @change="addUpload" multiple="multiple" accept="video/mp4,video/avi,video/mpg,video/mpeg,video/ram,.flv,video/mov,video/asf,video/3gp,video/f4v,video/wmv,video/x-ms-wmv" style="display: none;" id="file">
            <button class="solid_btn" @click="upimg">本地上传</button>
          </div>
        </div>
        <div class="image_list">
          <div class="left_list">
            <div data-v-1300c342="" class="title">已选视频</div>
            <ul class="">
              <li class="img" v-for="(item, index) in videoList" :key="index">
                <div class="oper">
                  <i class="iconfont gb" title="从库移除" @click="delVideo(item.videoNo, 1)">&#xe68c;</i>
                </div>
                <div class="icon iconfont" :title="item.videoName">&#xe690;</div>
                <a href="javascript:" :title="item.videoName">{{item.videoName}}</a>
              </li>
            </ul>
          </div>
          <div class="right_list">
            <div data-v-1300c342="" class="title">视频库列表</div>
            <ul>
              <li class="img" v-for="(item, index) in chapterVideoList" :key="index" @click="selVideo(item)" title="点击选择">
                <div class="oper">
                  <i class="iconfont gb" title="从库移除" @click.stop="delVideo(item.videoNo, 0, item.id)">&#xe68c;</i>
                </div>
                <div class="icon iconfont" :title="item.videoName">&#xe690;</div>
                <a href="javascript:" :title="item.videoName">{{item.videoName}}</a>
              </li>
                <div  @click="goVideo(index)" @mousemove="isShow=true" @mouseout="isShow=false" v-for="(item, index) in uploadList"  :key="index + 'ls'">
                  <li class="img">
                    <!-- <p v-show="isShow">鼠标移入移出</p> -->
                    <div class="el-icon-circle-close"></div>
                <div class="tip">{{item.tip}}</div>
                <div class="progress"><div class="entity" :style="{width: item.jd + '%'}"></div></div>
                <img :src="item.wait" alt="">
                <a href="javascript:">{{item.name}}+{{index}}</a>
              </li>
                </div>
            </ul>
          </div>
        </div>
      </div>
      <div class="modal_foot">
        <button class="solid_btn" @click="submit">确定</button>
        <button class="hollow_btn" @click="close()">取消</button>
      </div>
    </div>
  </div>
</template>
<script>
import {uploadResVideo} from '~/api/upload.js'
import {periodVideoUpdate, chapterVideoSave, periodVideo, chapterVideo, chapterVideodel} from '~/api/account/course.js'
export default {
  props: {
    open: {
      type: Boolean,
      default: false
    },
    data: {
      type: [Object, String],
      default: null
    },
    type: {
      // 1问题图片，2问题答案，3习题图片，4习题答案
      type: [String, Number],
      default: 1
    }
  },
  data () {
    return {
      flag: true,
      kg: true,
      upbtn: true,
      chapterVideoList: [],
      videoList: [],
      videoType: 'video/mp4,video/avi,video/mpg,video/mpeg,video/ram,video/flv,video/mov,video/asf,video/3gp,video/f4v,video/wmv,video/x-ms-wmv',
      savePic: {
        title: '',
        periodNo: 0,
        picType: this.type,
        picUrl: ''
      },
      imgsrc: '',
      uploadList: [],
      videoId:'', //VIDEO接口请求后拿到的ID
      isShow:false
    }
  },
  methods: {
    // 根据下标跳转至视频地址
    goVideo(index){
      let url=`http://dev52.hbck.com.cn:97/home/roncoo/file/video/${this.videoId}.${this.uploadList[index].name.split('.')[1]}`
     window.location.href = url
    },
    // 选择上传视频
    upimg () {
      let myfile = document.getElementById('file');
      myfile.click();
      console.log(this.uploadList+'+++++++++')
    //  console.log(`http://dev52.hbck.com.cn:97/${this.videoId}${this.videoId+this.uploadList[1].name.split('.')[1]}`)
    },
    // 选择视频
    selVideo (obj) {
      if (this.videoList && this.videoList.length) {
        this.$msgBox({
          content: '只能选择一个视频',
          isShowCancelBtn: false
        })
        return
      }
      this.videoList = [obj];
    },
    // 加入上传列表
    addUpload (e) {
      let files = e.target.files;
      for (var i = 0; i < files.length; i++) {
        let file = files[i];
        file.tip = '等待上传';
        file.jd = 0;
        this.uploadList.push(file);
      }
      if (this.upbtn) {
        this.upload();
      }
    },
    // 上传视频
    upload () {
      this.upbtn = false;
      let pics = this.uploadList;
      let itemfile = null;
      for (var i = 0; i < pics.length; i++) {
        if (pics[i].jd === 0 && itemfile === null) {
          itemfile = pics[i];
        }
      }
      if (itemfile) {
        let file = itemfile;
        let that = this;
        /* eslint-disable no-undef */
        let param = new FormData();
        param.append('videoFile', file, file.name);
        uploadResVideo(param, function (int) {
          itemfile.jd = int;
          itemfile.tip = '上传中';
          that.uploadList = Object.assign([], that.uploadList);
        }).then(res => {
          console.log('uploadResVideo',res)
          if (res.code === 200) {
            this.videoId=res.data//拿到视频ID地址
            console.log('this.videoId',this.videoId)
            console.log('uploadList',this.uploadList);//上传视频的全部数据列
            itemfile.jd = 100
            itemfile.tip = '上传成功';
            that.upload();
            that.savaVideo(res.data, itemfile.name);
          } else {
            itemfile.tip = res.msg;
            that.upload();
          }
          that.uploadList = pics;
        }).catch(msg => {
          itemfile.tip = '上传失败';
          that.upload();
        })
      } else {
        this.upbtn = true;
      }
    },
    // 保存视频
    savaVideo (vid, tit) {
      chapterVideoSave({
        chapterId: this.data.cNo,
        videoNo: vid
      }).then(res => {
        res = res.data;
        if (res.code === 200) {
          this.getChapterVideo();
        }
      })
    },
    // 删除视频
    delVideo (vNo, ty, id) {
      // let that = this;
      if (ty) {
        this.$msgBox({
          content: '你确定要删除该视频吗?'
        }).then(() => {
          for (let i = 0; i < this.videoList.length; i++) {
            if (this.videoList[i].videoNo === vNo) {
              this.videoList.splice(i, 1)
            }
          }
        })
      } else {
        this.flag = true
        for (let i = 0; i < this.videoList.length; i++) {
          if (this.videoList[i].videoNo === vNo) {
            this.flag = false
          }
        }
        if (this.flag) {
          this.$msgBox({
            content: '你确定要删除该视频吗?'
          }).then(() => {
            chapterVideodel({id}).then(res => {
              res = res.data;
              if (res.code === 200) {
                // that.alertOpen = false;
                this.$msgBox({
                  content: '删除成功',
                  isShowCancelBtn: false
                })
                this.getChapterVideo();
              } else {
                alert(res.msg);
              }
            })
          }).catch(() => {
            // console.log('取消删除')
          })
        } else {
          this.$msgBox({
            content: '该视频已被使用，请先删除',
            isShowCancelBtn: false
          })
        }
      }
    },
    // 提交保存选中视频
    submit () {
      let videoNo = this.videoList && this.videoList.length ? this.videoList[0].videoNo : ''
      periodVideoUpdate({videoNo, periodId: this.data.pNo}).then(res => {
        res = res.data;
        if (res.code === 200) {
          this.$emit('hidefun', event);
        } else {
          this.$msgBox({
            content: res.msg,
            isShowCancelBtn: false
          })
        }
      }).catch(() => {
        this.$msgBox({
          content: '提交失败',
          isShowCancelBtn: false
        })
      })
      // this.$emit('hidefun', event);
    },
    close () {
      if (this.upbtn) {
        this.$emit('hidefun', event);
      } else {
        this.$msgBox({
          content: '视频正在上传,确定关闭上传窗口吗?'
        }).then(res => {
          this.$emit('hidefun', event);
        })
      }
    },
    btnClick (event) {
      this.$emit('btnClick', event);
    },
    changeTab (int) {
      this.$emit('change', int);
    },
      // 获取该例题已选中的图片
    periodVideoList () {
      periodVideo({periodId: this.data.pNo}).then(res => {
        res = res.data
        if (res.code === 200 && res.data.list) {
          this.videoList = res.data.list
        } else {
          if (res.code > 300 && res.code < 400) {
            this.$msgBox({
              content: '登录超时，请重新登录',
              isShowCancelBtn: false
            }).then(() => {
              this.$store.dispatch('REDIRECT_LOGIN', result.code)
            }).catch(() => {
              this.$store.dispatch('REDIRECT_LOGIN', result.code)
            })
          }
          this.videoList = [];
        }
      });
    },
    getChapterVideo () {
      chapterVideo({chapterId: this.data.cNo}).then(res => {
        res = res.data;
        if (res.code === 200 && res.data.list !== null) {
          this.chapterVideoList = res.data.list;
          console.log('chapterVideoList',this.chapterVideoList)
        } else {
          if (res.code > 300 && res.code < 400) {
            this.$msgBox({
              content: '登录超时，请重新登录',
              isShowCancelBtn: false
            }).then(() => {
              this.$store.dispatch('REDIRECT_LOGIN', result.code)
            }).catch(() => {
              this.$store.dispatch('REDIRECT_LOGIN', result.code)
            })
          }
          this.chapterVideoList = [];
        }
      });
    }
  },
  mounted () {
    this.kg = false;
    this.periodVideoList();
    this.getChapterVideo();
  },
}
</script>
<style lang="scss"  rel="stylesheet/scss">
#image_panel_importance {
  width: 720px;
  margin-left: -360px;
}
.el-icon-circle-close{
  width: 30px;
  height: 30px;
}

.image_panel{
  width: 720px;
  margin-left: -360px;
  .modal_body{
    overflow: hidden;
  }
  .upload_box{
    line-height: 36px;
    padding: 10px 20px;
    border-bottom: 1px solid #e7e7eb;
    span{
      margin-right: 12px;
      color: #666;
    }
  }
  .modal_foot{
    border-top: 1px solid #e7e7eb;
    padding: 20px;
    text-align: center;
  }
  .image_list{
    overflow: hidden;
    height: 403px;
    .title{
      line-height: 30px;
      padding: 0 20px;
      font-size: 14px;
      background-color: #eee;
    }
    .left_list{
      float: left;
      width: 187px;
      border-right: 2px solid #ddd;
    }
    .right_list{
      float: right;
      width: 531px;
    }
    ul{
      height: 373px;
      overflow: auto;
    }
    .icon{
      text-align: center;
      line-height: 80px;
      font-size: 50px;
      color: #999;
    }
    .img{
      position: relative;
      float: left;
      margin-left: 16px;
      margin-top: 16px;
      width: 153px;
      border: 1px solid #e7e7eb;
      .oper{
        display: none;
      }
      .gb{
        position: absolute;
        right: -10px;
        top: -10px;
        font-size: 18px;
        cursor: pointer;
        color: #666;
      }
      img{
        display: block;
        height: 80px;
      }
      a{
        display: block;
        text-align: center;
        line-height: 30px;
        border-top: 1px solid #e7e7eb;
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
      }
      .sel{
        color: #eee;
        margin: 0 3px;
      }
      &:hover{
        border-color: #ed7d03;
        .oper{
          display: block;
        }
        .icon {
          color: #ed7d03;
        }
      }
    }
    .tip{
      text-align: center;
      font-size: 16px;
      line-height: 60px;
    }
    .progress,.tip,.oper{
      position: absolute;
      left: 0;
      top: 0;
      right: 0;
      z-index: 9;
      height: 80px;
      .entity{
        height: 100%;
        // background-color: rgba(0, 204, 0, 0.59);
      }
    }
  }
}
.max_img_panel{
  position: relative;
  z-index: 1000;
  .img_panel{
    display: table;
    position: fixed;
    z-index: 999;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
  }
  .img_box{
    text-align: center;
    display: table-cell;
    vertical-align: middle;
  }
  .img{
    display: inline-block;
    position: relative;
    max-width: 80%;
    i{
      position: absolute;
      right: 0;
      top: 0;
      z-index: 9;
      font-size: 30px;
      cursor: pointer;
      &:hover{
        color: #e00;
      }
    }
  }
}
</style>
