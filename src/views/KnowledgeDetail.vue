<!-- 农业知识详情 -->
<template>
  <div class="knowlege-detail-container">
    <div class="title">{{ updateInfo.title }}</div>
    <div class="tips">
      <span>作者：</span>
      <span style="margin-right: 20px">{{ updateInfo.ownName }}</span>
      <span>日期：</span>
      <span>{{ updateInfo.updateTime | formatTimer }}</span>
    </div>
    <div class="detail-img">
      <video
        v-if="updateInfo.type === 'mp4' || updateInfo.type === 'MP4'"
        id="video"
        width="900"
        height="360"
        :src="$store.state.imgShowRoad + '/file/' + updateInfo.picPath"
        controls
      ></video>
      <img
        v-else
        style="width: 500px; height: 300px"
        :src="$store.state.imgShowRoad + '/file/' + updateInfo.picPath"
        alt=""
      />
    </div>
    <div class="detail-content">
      <pre>{{ updateInfo.content }}</pre>
    </div>

    <!-- <quill-editor  class="editor" v-model="content"  ref="myQuillEditor" :options="editorOption" @blur="onEditorBlur($event)" @focus="onEditorFocus($event)" @change="onEditorChange($event)">
    </quill-editor> -->
    <el-input type="textarea" v-model="content" :rows="5"></el-input>
    <!-- <div>{{ content }}</div> -->
    <div style="margin-top: 20px; display: flex; flex-direction: row; justify-content: flex-end">
      <el-button type="success" @click="handleComment">添加评论</el-button>
    </div>
    <div class="comment-container">
      <div class="comment-num">评论共{{ commentArray.length || 0 }}条</div>
      <div class="comment-item" v-for="(item, index) in commentArray" :key="index">
        <div>{{ item.content }}</div>
        <div class="comment-tips">
          <div style="margin-right: 40px">
            评论人：<span class="color6">{{ item.ownName }}</span>
          </div>
          <div>
            评论时间：<span class="color6">{{ item.createTime | formatTimer2 }}</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { selectKnowledgeById, selectComment, addComment } from '../api/knowledge'

// require styles 引入样式
import 'quill/dist/quill.core.css'
import 'quill/dist/quill.snow.css'
import 'quill/dist/quill.bubble.css'
import { quillEditor } from 'vue-quill-editor'
// 给quill-editor组件中的工具栏中的工具添加title（鼠标移入时显示）
import { addQuillTitle } from '../utils/quill_title'
import ImageResize from 'quill-image-resize-module'
Quill.register('modules/imageResize', ImageResize)

// 工具栏配置
const toolbarOptions = [
  ['bold', 'italic', 'underline', 'strike'], // 加粗 斜体 下划线 删除线 -----['bold', 'italic', 'underline', 'strike']
  ['blockquote', 'code-block'], // 引用  代码块-----['blockquote', 'code-block']
  [{ header: 1 }, { header: 2 }], // 1、2 级标题-----[{ header: 1 }, { header: 2 }]
  [{ list: 'ordered' }, { list: 'bullet' }], // 有序、无序列表-----[{ list: 'ordered' }, { list: 'bullet' }]
  [{ script: 'sub' }, { script: 'super' }], // 上标/下标-----[{ script: 'sub' }, { script: 'super' }]
  [{ indent: '-1' }, { indent: '+1' }], // 缩进-----[{ indent: '-1' }, { indent: '+1' }]
  [{ direction: 'rtl' }], // 文本方向-----[{'direction': 'rtl'}]
  [{ size: ['small', false, 'large', 'huge'] }], // 字体大小-----[{ size: ['small', false, 'large', 'huge'] }]
  [{ header: [1, 2, 3, 4, 5, 6, false] }], // 标题-----[{ header: [1, 2, 3, 4, 5, 6, false] }]
  [{ color: [] }, { background: [] }], // 字体颜色、字体背景颜色-----[{ color: [] }, { background: [] }]
  [{ font: [] }], // 字体种类-----[{ font: [] }]
  [{ align: [] }], // 对齐方式-----[{ align: [] }]
  ['clean'], // 清除文本格式-----['clean']
  ['image'] // 链接、图片、视频-----['link', 'image', 'video']
]

export default {
  data() {
    return {
      updateInfo: {},
      content: '',
      commentArray: [],
      editorOption: {
        placeholder: '请输入文本...',
        theme: 'snow',
        modules: {
          toolbar: {
            container: toolbarOptions
          },
          imageResize: {
            //调整大小组件。
            displayStyles: {
              backgroundColor: 'black',
              border: 'none',
              color: 'white'
            },
            modules: ['Resize', 'DisplaySize', 'Toolbar']
          }
        }
      }
    }
  },
  // components: { quillEditor},
  filters: {
    formatTimer: function (value) {
      let date = new Date(value)
      let y = date.getFullYear()
      let MM = date.getMonth() + 1
      MM = MM < 10 ? '0' + MM : MM
      let d = date.getDate()
      d = d < 10 ? '0' + d : d
      let h = date.getHours()
      h = h < 10 ? '0' + h : h
      let m = date.getMinutes()
      m = m < 10 ? '0' + m : m
      let s = date.getSeconds()
      s = s < 10 ? '0' + s : s
      return y + '-' + MM + '-' + d + ' '
    },
    formatTimer2: function (value) {
      let date = new Date(value)
      let y = date.getFullYear()
      let MM = date.getMonth() + 1
      MM = MM < 10 ? '0' + MM : MM
      let d = date.getDate()
      d = d < 10 ? '0' + d : d
      let h = date.getHours()
      h = h < 10 ? '0' + h : h
      let m = date.getMinutes()
      m = m < 10 ? '0' + m : m
      let s = date.getSeconds()
      s = s < 10 ? '0' + s : s
      return y + '-' + MM + '-' + d + ' ' + h + ':' + m
    }
  },
  methods: {
    onEditorBlur() {},
    onEditorFocus() {
      // 获得焦点事件
    },
    onEditorChange() {
      // 内容改变事件
      this.onEditorChange()
      {
      }
    },
    getData() {
      this.$store.commit('updateActiveIndex', '4')
      selectKnowledgeById({
        knowledgeId: this.$route.params.id
      })
        .then((res) => {
          let tmp = res.data
          const flieArr = tmp.picPath.split('.')
          tmp.type = flieArr[flieArr.length - 1]
          this.updateInfo = tmp
          console.log('this.updateInfo', this.updateInfo)
        })
        .catch((err) => {
          console.log(err)
        })
    },
    // 查询评论
    getCommentData() {
      selectComment({
        knowledgeId: this.$route.params.id
      })
        .then((res) => {
          this.commentArray = res.data
        })
        .catch((err) => {
          console.log(err)
        })
    },
    handleComment() {
      if (this.content === '') {
        this.$message.error('评论内容不能为空！')
        return
      }
      if (localStorage.getItem('token')) {
        addComment({
          knowledgeId: this.$route.params.id,
          content: this.content
        })
          .then((res) => {
            this.content = ''
            this.$message.success('评论成功！')
            this.getCommentData()
          })
          .catch((err) => {
            console.log(err)
          })
      } else {
        this.$message.error('请先登录')
      }
    }
  },
  mounted() {
    // addQuillTitle();
    this.getData()
    this.getCommentData()
  }
}
</script>

<style lang="less" scoped>
.editor {
  line-height: normal !important;
  /* height: 400px; */
  /* border: 1px solid #DCDFE6; */
}
/* 调节文本框最小高度 */
.ql-container.ql-snow {
  min-height: 150px;
}

.ql-snow .ql-tooltip[data-mode='link']::before {
  content: '请输入链接地址:';
}
.ql-snow .ql-tooltip.ql-editing a.ql-action::after {
  border-right: 0px;
  content: '保存';
  padding-right: 0px;
}

.ql-snow .ql-tooltip[data-mode='video']::before {
  content: '请输入视频地址:';
}

.ql-snow .ql-picker.ql-size .ql-picker-label::before,
.ql-snow .ql-picker.ql-size .ql-picker-item::before {
  content: '14px';
}
.ql-snow .ql-picker.ql-size .ql-picker-label[data-value='small']::before,
.ql-snow .ql-picker.ql-size .ql-picker-item[data-value='small']::before {
  content: '10px';
}
.ql-snow .ql-picker.ql-size .ql-picker-label[data-value='large']::before,
.ql-snow .ql-picker.ql-size .ql-picker-item[data-value='large']::before {
  content: '18px';
}
.ql-snow .ql-picker.ql-size .ql-picker-label[data-value='huge']::before,
.ql-snow .ql-picker.ql-size .ql-picker-item[data-value='huge']::before {
  content: '32px';
}

.ql-snow .ql-picker.ql-header .ql-picker-label::before,
.ql-snow .ql-picker.ql-header .ql-picker-item::before {
  content: '文本';
}
.ql-snow .ql-picker.ql-header .ql-picker-label[data-value='1']::before,
.ql-snow .ql-picker.ql-header .ql-picker-item[data-value='1']::before {
  content: '标题1';
}
.ql-snow .ql-picker.ql-header .ql-picker-label[data-value='2']::before,
.ql-snow .ql-picker.ql-header .ql-picker-item[data-value='2']::before {
  content: '标题2';
}
.ql-snow .ql-picker.ql-header .ql-picker-label[data-value='3']::before,
.ql-snow .ql-picker.ql-header .ql-picker-item[data-value='3']::before {
  content: '标题3';
}
.ql-snow .ql-picker.ql-header .ql-picker-label[data-value='4']::before,
.ql-snow .ql-picker.ql-header .ql-picker-item[data-value='4']::before {
  content: '标题4';
}
.ql-snow .ql-picker.ql-header .ql-picker-label[data-value='5']::before,
.ql-snow .ql-picker.ql-header .ql-picker-item[data-value='5']::before {
  content: '标题5';
}
.ql-snow .ql-picker.ql-header .ql-picker-label[data-value='6']::before,
.ql-snow .ql-picker.ql-header .ql-picker-item[data-value='6']::before {
  content: '标题6';
}

.ql-snow .ql-picker.ql-font .ql-picker-label::before,
.ql-snow .ql-picker.ql-font .ql-picker-item::before {
  content: '标准字体';
}
.ql-snow .ql-picker.ql-font .ql-picker-label[data-value='serif']::before,
.ql-snow .ql-picker.ql-font .ql-picker-item[data-value='serif']::before {
  content: '衬线字体';
}
.ql-snow .ql-picker.ql-font .ql-picker-label[data-value='monospace']::before,
.ql-snow .ql-picker.ql-font .ql-picker-item[data-value='monospace']::before {
  content: '等宽字体';
}

.knowlege-detail-container {
  width: 1100px;
  margin: 0 auto;
  background: #fff;
  min-height: 100%;
  padding: 10px 20px;
  margin-top: 10px;
  .title {
    font-size: 18px;
    text-align: center;
  }
  .tips {
    color: #999;
    display: flex;
    justify-content: flex-end;
    height: 30px;
    align-items: center;
  }
  .detail-img {
    display: flex;
    justify-content: center;
    margin-top: 50px;
    video {
      border: 1px solid #f2f2f2;
    }
    img {
      border-radius: 6px;
    }
  }
  .detail-content {
  }
  .comment-container {
    clear: both;
    margin-top: 10px;
    .comment-num {
      font-weight: bold;
    }
    .comment-item {
      border-bottom: 1px solid #f2f2f2;
      padding: 10px 20px;
      margin: 20px 0;
      word-break: break-all;
      .comment-tips {
        display: flex;
        flex-direction: row;
        justify-content: flex-end;
        .color6 {
          color: #666;
        }
      }
    }
  }
}
</style>