<template>
  <div class="goodsEvaluate" ref="goodsEvaluateRef">
    <div class="myEvaluate">
      <div class="rate">
        <span>打分</span>
        <el-rate v-model="myComment.rate" :colors="colors" />
      </div>
      <textarea
        v-model="myComment.commentContent"
        name=""
        id=""
        cols="80"
        rows="3"
      ></textarea>
      <button @click="comment">我要评价</button>
    </div>
    <ul class="list" v-for="(v, i) in commentList" :key="i">
      <img :src="v.avatar" alt="" />
      <li class="info">
        <div class="info-top">
          <div class="info-top-l">
            <span class="nickname">{{ formatName(v.name) }}</span>

            <p class="ip">
              IP: <span>{{ v.ip }}</span>
            </p>
          </div>
          <el-rate v-model="v.rate" :disabled="true" :colors="colors" />
        </div>
        <p class="evaluate">
          {{ v.commentContent }}
        </p>
        <div class="info-b">
          <span class="time">{{ v.commentDate }}</span>
        </div>
      </li>
    </ul>

    <!-- 弹窗 -->
    <div class="dialog" v-if="dialogShow">
      <div class="main">
        <p>请登录后再进行评价</p>
        <el-button @click="confirm">确定</el-button>
      </div>
    </div>

    <div>
      <el-result v-if="resShow" icon="success" title="评论成功"> </el-result>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref, reactive } from 'vue'
import router from '@/router'
import { formatName } from '@/utils/formatName'
import usePinia from '@/store'

const props = withDefaults(
  defineProps<{
    commentList: any[]
    id: any
  }>(),
  {},
)
const emits = defineEmits(['addComment'])
const { user } = usePinia()
const colors = ref(['#99A9BF'])

//是否显示结果标识
const resShow = ref(false)
// 是否显示弹窗标识
const dialogShow = ref(false)
const token = user.token
// 我的评论数据
const myComment = reactive({
  rate: 0,
  commentContent: '',
  commodityMessageId: props.id,
  userId: user.userId,
})

// 评论
const comment = () => {
  // 如果没登录，展示弹窗
  if (!token) {
    dialogShow.value = true
  }

  emits('addComment', myComment)

  // 评论完成， 把输入框的内容清空
  setTimeout(() => {
    myComment.commentContent = ''
    myComment.rate = 0
  }, 500)

  // 结果弹窗显示 1秒后隐藏
  resShow.value = true
  setTimeout(() => {
    resShow.value = false
  }, 1000)
}

const confirm = () => {
  dialogShow.value = false
  router.push('/login')
}

const goodsEvaluateRef = ref<InstanceType<typeof HTMLDivElement>>()
defineExpose({ goodsEvaluateRef })
</script>

<style lang="less" scoped>
@import '@/style/common.less';

.goodsEvaluate {
  border: 1px solid @basic-color;
  height: 100%;
  .myEvaluate {
    width: 80%;
    height: 100px;
    margin-top: 20px;
    margin-left: 72px;
    .flex;
    &:not(:last-child) {
      border-bottom: 2px solid @basic-color;
    }
    .rate {
      display: flex;
      flex-direction: column;
      align-items: center;
      span {
        font-weight: bold;
        color: @basic-color;
      }
    }
    textarea {
      outline: 0;
      border: 1px solid #ddd;
      &:focus {
        border: 1px solid blue;
        box-shadow: 0 0 6px @basic-color;
      }
    }
    button {
      width: 100px;
      height: 40px;
      background-color: @basic-color;
      color: #fff;
      border: 0;
      border-radius: 5px;
      cursor: pointer;
    }
  }
  .list {
    padding: 20px;
    padding-left: 100px;
    display: flex;
    position: relative;
    // &:not(:last-child) {
    //   border-bottom: 2px solid @basic-color;
    // }
    &:not(:last-child)::after {
      content: '';
      position: absolute;
      width: 80%;
      height: 1px;
      background-color: @basic-color;
      bottom: 0;
      left: 6%;
    }
    img {
      width: 49px;
      height: 49px;
      border-radius: 7px;
    }
    .info {
      width: 75%;
      margin-left: 30px;

      .info-top {
        .flex(@align:start);
        .info-top-l {
          display: flex;
          align-items: center;
          .nickname {
            color: rgb(91, 91, 205);
            margin-right: 10px;
          }
          .level {
            margin-right: 10px;
          }

          .level,
          .ip {
            color: rgb(149, 147, 147);
          }
        }
      }
      .evaluate {
        line-height: 25px;
        margin-bottom: 3px;
      }
      .info-b {
        margin-top: 10px;
        .flex;
        .view {
          font-size: 12px;
          .num {
            color: coral;
            font-size: 13px;
            font-weight: bold;
          }
        }
        .time {
          text-align: right;
          font-size: 14px;
          color: coral;
        }
      }
    }
  }
  .icon-zuanshi {
    color: @basic-color;
  }
}
.dialog {
  width: 100vw;
  height: 100vh;
  background-color: rgba(149, 126, 126, 0.404);
  position: fixed;
  left: 0;
  top: 0;
  z-index: 1000;
  .main {
    width: 300px;
    height: 100px;
    box-shadow: 0 0 10px #ddd;
    background-color: #fff;
    position: absolute;
    left: 50%;
    top: 40%;
    transform: translateX(-50%);
    p {
      text-align: center;
      line-height: 40px;
      color: red;
    }
    button {
      position: absolute;
      right: 20px;
      bottom: 10px;
      background-color: @basic-color;
      color: #fff;
    }
  }
}
.el-result {
  position: fixed;
  left: 50%;
  top: 40%;
  transform: translateX(-50%);
  background-color: #fff;
  box-shadow: 0 0 5px #ddd;
}
</style>
