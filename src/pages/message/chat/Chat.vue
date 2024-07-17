<template>
  <div class="Chat">
    <div class="chat-content" @touchstart="data.tooltipTop = -1">
      <div class="header">
        <div class="left">
          <dy-back @click="router.back"></dy-back>
          <div class="badge">6</div>
          <span>章若楠</span>
        </div>
        <div class="right">
          <img
            @click="bus.emit(EVENT_KEY.SHOW_AUDIO_CALL)"
            src="../../../assets/img/icon/message/chat/call.png"
            alt=""
          />
          <img @click="_no" src="../../../assets/img/icon/message/chat/video-white.png" alt="" />
          <img
            src="../../../assets/img/icon/menu-white.png"
            alt=""
            @click="nav('/message/chat/detail')"
          />
        </div>
      </div>
      <div class="message-wrapper" ref="msgWrapper" :class="isExpand ? 'expand' : ''">
        <ChatMessage
          @itemClick="clickItem"
          v-longpress="showTooltip"
          :message="item"
          :key="index"
          v-for="(item, index) in data.messages"
        ></ChatMessage>
      </div>
      <div class="footer">
        <div class="toolbar" v-if="!data.recording">
          <img src="../../../assets/img/icon/message/camera.png" alt="" class="camera" />
          <input
            @click="data.typing = true"
            @blur="data.typing = false"
            type="text"
            placeholder="发送信息..."
          />
          <img @click="handleClick" src="../../../assets/img/icon/message/voice-white.png" alt="" />
          <img src="../../../assets/img/icon/message/emoji-white.png" alt="" />
          <img
            @click="data.showOption = !data.showOption"
            src="../../../assets/img/icon/message/add-white.png"
            alt=""
          />
        </div>
        <div class="record" v-else>
          <span>按住 说话</span>
          <img
            @click="data.recording = false"
            src="../../../assets/img/icon/message/keyboard.png"
            alt=""
          />
        </div>
        <div class="options" v-if="data.showOption">
          <div class="option-wrapper">
            <div class="option">
              <img src="../../../assets/img/icon/message/photo.png" alt="" />
              <span>照片</span>
            </div>
            <div class="option">
              <img src="../../../assets/img/icon/message/camera2.png" alt="" />
              <span>拍摄</span>
            </div>
            <div class="option">
              <img src="../../../assets/img/icon/message/redpack.png" alt="" />
              <span>红包</span>
            </div>
            <div class="option">
              <img src="../../../assets/img/icon/message/video.png" alt="" />
              <span>视频通话</span>
            </div>
            <div class="option">
              <img src="../../../assets/img/icon/message/audio.png" alt="" />
              <span>语音通话</span>
            </div>
            <div class="option">
              <img src="../../../assets/img/icon/message/come-on.png" alt="" />
              <span>一起看视频</span>
            </div>
            <div class="option">
              <img src="../../../assets/img/icon/message/come-chang.png" alt="" />
              <span>一起唱</span>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!--  长按工具栏  -->
    <transition name="tooltip">
      <!--      TODO 定位也有问题-->
      <div class="tooltip" :style="{ top: data.tooltipTop + 'px' }" v-if="data.tooltipTop !== -1">
        <div class="options">
          <img src="../../../assets/img/icon/message/chat/like.png" alt="" />
          <span>点赞</span>
        </div>
        <div class="options">
          <img src="../../../assets/img/icon/message/chat/copy.png" alt="" />
          <span>复制</span>
        </div>
        <div class="options">
          <img src="../../../assets/img/icon/message/chat/send.png" alt="" />
          <span>转发</span>
        </div>
        <div class="options">
          <img src="../../../assets/img/icon/message/chat/comment.png" alt="" />
          <span>回复</span>
        </div>
        <div class="options">
          <img src="../../../assets/img/icon/message/chat/return.png" alt="" />
          <span>回复</span>
        </div>
        <div class="options">
          <img src="../../../assets/img/icon/message/chat/del.png" alt="" />
          <span>删除</span>
        </div>
        <!--      TODO 官方的三角头会随着点击位置变动，先注释掉-->
        <!--      <div class="arrow" :class="tooltipTopLocation"></div>-->
      </div>
    </transition>

    <div class="preview-img" v-if="false">
      <div class="header">
        <dy-back mode="light" />
        <img src="../../../assets/img/icon/search-light.png" alt="" />
      </div>
      <img :src="data.previewImg" alt="" class="img-src" />
      <div class="footer"></div>
    </div>

    <!--  红包  -->
    <transition name="scale">
      <div class="red-packet" v-if="data.isShowOpenRedPacket">
        <BaseMask @click="data.isShowOpenRedPacket = false" />
        <div class="content">
          <template v-if="data.isOpened">
            <img src="../../../assets/img/icon/message/chat/bg-open.png" alt="" class="bg" />
            <div class="wrapper">
              <div class="top">
                <div class="money">13.14元</div>
                <div class="belong">{{ store.userinfo.nickname }}的红包</div>
                <div class="password">大吉大利</div>
              </div>
              <div class="notice" @click="nav('/message/chat/red-packet-detail')">
                查看红包详情>
              </div>
            </div>
          </template>
          <template v-else>
            <img src="../../../assets/img/icon/message/chat/bg-close.png" alt="" class="bg" />
            <div class="wrapper">
              <div class="top">
                <img
                  :src="_checkImgUrl(store.userinfo.cover_url[0].url_list[0])"
                  alt=""
                  class="avatar"
                />
                <div class="belong">{{ store.userinfo.nickname }}的红包</div>
                <div class="password">大吉大利</div>
              </div>

              <div class="l-button" :class="{ opening: data.opening }" @click="openRedPacket">
                <template v-if="data.opening">
                  <img src="../../../assets/img/icon/loading-white.png" alt="" />
                  正在打开
                </template>
                <span v-else>开红包</span>
              </div>
            </div>
          </template>
          <img
            src="../../../assets/img/icon/message/chat/close.png"
            alt=""
            class="close"
            @click="data.isShowOpenRedPacket = false"
          />
        </div>
      </div>
    </transition>

    <Loading v-if="data.loading" />
  </div>
</template>
<script setup lang="ts">
import ChatMessage from '../components/ChatMessage.vue'
import { computed, nextTick, onMounted, onUnmounted, reactive, ref } from 'vue'
import Loading from '@/components/Loading.vue'
import { useBaseStore } from '@/store/pinia'
import { _checkImgUrl, _no, _sleep } from '@/utils'
import { useRouter } from 'vue-router'
import { useNav } from '@/utils/hooks/useNav'
import bus, { EVENT_KEY } from '@/utils/bus'

let CALL_STATE = {
  REJECT: 0,
  RESOLVE: 1,
  NONE: 2
}
let VIDEO_STATE = {
  VALID: 0,
  INVALID: 1
}
let AUDIO_STATE = {
  NORMAL: 0,
  SENDING: 1
}
let READ_STATE = {
  SENDING: 0,
  ARRIVED: 1,
  READ: 1
}
let MESSAGE_TYPE = {
  TEXT: 0,
  TIME: 1,
  VIDEO: 2,
  DOUYIN_VIDEO: 9,
  AUDIO: 3,
  IMAGE: 6,
  VIDEO_CALL: 4,
  AUDIO_CALL: 5,
  MEME: 7, //表情包
  RED_PACKET: 8 //红包
}
let RED_PACKET_MODE = {
  SINGLE: 1,
  MULTIPLE: 2
}

defineOptions({
  name: 'Chat'
})

const router = useRouter()
const nav = useNav()
const store = useBaseStore()
const msgWrapper = ref<HTMLDivElement>()
const data = reactive({
  previewImg: new URL('../../../assets/img/poster/3.jpg', import.meta.url).href,
  videoCall: [],
  MESSAGE_TYPE,
  messages: [
    {
      type: MESSAGE_TYPE.TIME,
      data: '',
      time: '2021-01-02 21:21',
      user: {
        id: '2739632844317827',
        avatar: '../../assets/img/icon/head-image.jpg'
      }
    },

    {
      type: MESSAGE_TYPE.MEME,
      state: AUDIO_STATE.NORMAL,
      data: new URL('../../../assets/img/poster/1.jpg', import.meta.url).href,
      time: '2021-01-02 21:21',
      user: {
        id: '2739632844317827',
        avatar: '../../assets/img/icon/head-image.jpg'
      },
      loved: [
        {
          id: 2,
          avatar: '../../assets/img/icon/head-image.jpg'
        },
        {
          id: 2,
          avatar: '../../assets/img/icon/head-image.jpg'
        }
      ]
    },
    {
      type: MESSAGE_TYPE.RED_PACKET,
      state: AUDIO_STATE.NORMAL,
      mode: RED_PACKET_MODE.MULTIPLE,
      data: {
        money: 5.11,
        title: '大吉大利',
        state: '未领取'
      },
      time: '2021-01-02 21:21',
      user: {
        id: '2739632844317827',
        avatar: '../../assets/img/icon/head-image.jpg'
      }
    },
    {
      type: MESSAGE_TYPE.RED_PACKET,
      state: AUDIO_STATE.NORMAL,
      mode: RED_PACKET_MODE.SINGLE,
      data: {
        money: 5.11,
        title: '大吉大利',
        state: '已过期'
      },
      time: '2021-01-02 21:21',
      user: {
        id: 1,
        avatar: '../../assets/img/icon/head-image.jpg'
      }
    },
    {
      type: MESSAGE_TYPE.IMAGE,
      state: AUDIO_STATE.NORMAL,
      data: new URL('../../../assets/img/poster/1.jpg', import.meta.url).href,
      time: '2021-01-02 21:21',
      user: {
        id: 1,
        avatar: '../../assets/img/icon/head-image.jpg'
      }
    },
    {
      type: MESSAGE_TYPE.IMAGE,
      state: AUDIO_STATE.NORMAL,
      data: new URL('../../../assets/img/poster/1.jpg', import.meta.url).href,
      time: '2021-01-02 21:21',
      user: {
        id: '2739632844317827',
        avatar: '../../assets/img/icon/head-image.jpg'
      },
      readState: READ_STATE.ARRIVED
    },
    {
      type: MESSAGE_TYPE.VIDEO_CALL,
      state: CALL_STATE.REJECT,
      data: '2021-01-02 21:44',
      time: '2021-01-02 21:21',
      user: {
        id: '2739632844317827',
        avatar: '../../assets/img/icon/head-image.jpg'
      }
    },
    {
      type: MESSAGE_TYPE.VIDEO_CALL,
      state: CALL_STATE.RESOLVE,
      data: '2021-01-02 21:44',
      time: '2021-01-02 21:21',
      user: {
        id: '2739632844317827',
        avatar: '../../assets/img/icon/head-image.jpg'
      }
    },
    {
      type: MESSAGE_TYPE.VIDEO_CALL,
      state: CALL_STATE.NONE,
      data: '2021-01-02 21:44',
      time: '2021-01-02 21:21',
      user: {
        id: '2739632844317827',
        avatar: '../../assets/img/icon/head-image.jpg'
      }
    },
    {
      type: MESSAGE_TYPE.AUDIO_CALL,
      state: CALL_STATE.REJECT,
      data: '2021-01-02 21:44',
      time: '2021-01-02 21:21',
      user: {
        id: '2739632844317827',
        avatar: '../../assets/img/icon/head-image.jpg'
      }
    },
    {
      type: MESSAGE_TYPE.AUDIO_CALL,
      state: CALL_STATE.RESOLVE,
      data: '2021-01-02 21:44',
      time: '2021-01-02 21:21',
      user: {
        id: '2739632844317827',
        avatar: '../../assets/img/icon/head-image.jpg'
      }
    },
    {
      type: MESSAGE_TYPE.AUDIO_CALL,
      state: CALL_STATE.NONE,
      data: '2021-01-02 21:44',
      time: '2021-01-02 21:21',
      user: {
        id: '2739632844317827',
        avatar: '../../assets/img/icon/head-image.jpg'
      }
    },
    {
      type: MESSAGE_TYPE.AUDIO,
      state: AUDIO_STATE.NORMAL,
      data: {
        duration: 5,
        src: ''
      },
      time: '2021-01-02 21:21',
      user: {
        id: '1',
        avatar: '../../assets/img/icon/head-image.jpg'
      }
    },
    {
      type: MESSAGE_TYPE.AUDIO,
      state: AUDIO_STATE.NORMAL,
      data: {
        duration: 10,
        src: ''
      },
      time: '2021-01-02 21:21',
      user: {
        id: '2739632844317827',
        avatar: '../../assets/img/icon/head-image.jpg'
      }
    },
    {
      type: MESSAGE_TYPE.TEXT,
      data: '又在刷抖音',
      time: '2021-01-02 21:21',
      user: {
        id: '2739632844317827',
        avatar: '../../assets/img/icon/head-image.jpg'
      }
    },
    {
      type: MESSAGE_TYPE.TEXT,
      data: '我昨天@你那个视频发给我下',
      time: '2021-01-02 21:21',
      user: {
        id: '2739632844317827',
        avatar: '../../assets/img/icon/head-image.jpg'
      }
    },
    {
      type: MESSAGE_TYPE.TEXT,
      data: '是麦田那个吗?',
      time: '2021-01-02 21:21',
      user: {
        id: '1',
        avatar: '../../assets/img/icon/head-image.jpg'
      }
    },
    {
      type: MESSAGE_TYPE.TEXT,
      data: '那个地方叫做稻城',
      time: '2021-01-02 21:21',
      user: {
        id: '2739632844317827',
        avatar: '../../assets/img/icon/head-image.jpg'
      }
    },
    {
      type: MESSAGE_TYPE.DOUYIN_VIDEO,
      state: VIDEO_STATE.VALID,
      data: {
        poster: new URL('../../../assets/img/poster/1.jpg', import.meta.url).href,
        author: {
          name: '带你去旅行',
          avatar: new URL('../../../assets/img/icon/head-image.jpeg', import.meta.url).href
        },
        title: '只分享给你'
      },
      time: '2021-01-02 21:21',
      user: {
        id: '1',
        avatar: '../../../assets/img/icon/head-image.jpg'
      }
    },
    {
      type: MESSAGE_TYPE.TEXT,
      data: '哇,这就是电影里的稻城',
      time: '2021-01-02 21:21',
      user: {
        id: '1',
        avatar: '../../assets/img/icon/head-image.jpg'
      }
    },
    {
      type: MESSAGE_TYPE.TEXT,
      data: '真的好漂亮',
      time: '2021-01-02 21:21',
      user: {
        id: '1',
        avatar: '../../assets/img/icon/head-image.jpg'
      }
    },
    {
      type: MESSAGE_TYPE.TEXT,
      data: '对8',
      time: '2021-01-02 21:21',
      user: {
        id: '2739632844317827',
        avatar: '../../assets/img/icon/head-image.jpg'
      }
    },
    {
      type: MESSAGE_TYPE.TEXT,
      data: '那咱们什么时候去',
      time: '2021-01-02 21:21',
      user: {
        id: '1',
        avatar: '../../assets/img/icon/head-image.jpg'
      }
    },
    {
      type: MESSAGE_TYPE.TEXT,
      data: '下周我有空,下周吧',
      time: '2021-01-02 21:21',
      user: {
        id: '2739632844317827',
        avatar: '../../assets/img/icon/head-image.jpg'
      }
    },
    {
      type: MESSAGE_TYPE.TEXT,
      data: '期待👌',
      time: '2021-01-02 21:21',
      user: {
        id: '1',
        avatar: '../../assets/img/icon/head-image.jpg'
      }
    },
    {
      type: MESSAGE_TYPE.VIDEO,
      state: VIDEO_STATE.VALID,
      data: {
        poster: new URL('../../../assets/img/poster/3.jpg', import.meta.url).href
      },
      time: '2021-01-02 21:21',
      user: {
        id: '2739632844317827',
        avatar: '../../../assets/img/icon/head-image.jpg'
      }
    },
    {
      type: MESSAGE_TYPE.TEXT,
      data: '香草味的冰淇淋还有吗?',
      time: '2021-01-02 21:21',
      user: {
        id: '1',
        avatar: '../../assets/img/icon/head-image.jpg'
      }
    },
    {
      type: MESSAGE_TYPE.TEXT,
      data: '就知道你馋,冰箱里给你屯着呢',
      time: '2021-01-02 21:21',
      user: {
        id: '2739632844317827',
        avatar: '../../assets/img/icon/head-image.jpg'
      }
    },
    {
      type: MESSAGE_TYPE.TEXT,
      data: '少吃一点哈',
      time: '2021-01-02 21:21',
      user: {
        id: '1',
        avatar: '../../assets/img/icon/head-image.jpg'
      }
    },
    {
      type: MESSAGE_TYPE.TEXT,
      data: '在最下面那层',
      time: '2021-01-02 21:21',
      user: {
        id: '2739632844317827',
        avatar: '../../assets/img/icon/head-image.jpg'
      }
    },
    {
      type: MESSAGE_TYPE.TEXT,
      data: '我就吃亿点点',
      time: '2021-01-02 21:21',
      user: {
        id: '1',
        avatar: '../../assets/img/icon/head-image.jpg'
      }
    },
    {
      type: MESSAGE_TYPE.TEXT,
      data: '草莓味的也好吃,你可以尝尝',
      time: '2021-01-02 21:21',
      user: {
        id: '2739632844317827',
        avatar: '../../assets/img/icon/head-image.jpg'
      }
    },
    {
      type: MESSAGE_TYPE.TEXT,
      data: '你说的没错',
      time: '2021-01-02 21:21',
      user: {
        id: '1',
        avatar: '../../assets/img/icon/head-image.jpg'
      }
    },
    {
      type: MESSAGE_TYPE.TEXT,
      data: '这个确实很好吃',
      time: '2021-01-02 21:21',
      user: {
        id: '1',
        avatar: '../../assets/img/icon/head-image.jpg'
      }
    }
  ],
  typing: false,
  loading: false,
  opening: false,
  isOpened: false,
  recording: false,
  showOption: false,
  isShowOpenRedPacket: false,
  tooltipTop: -1,
  tooltipTopLocation: ''
})

onMounted(() => {
  msgWrapper.value
    .querySelectorAll('img')
    .forEach((item) => item.addEventListener('load', scrollBottom))
  scrollBottom()
})

onUnmounted(() => {
  msgWrapper.value
    .querySelectorAll('img')
    .forEach((item) => item.removeEventListener('load', scrollBottom))
})

const isExpand = computed(() => {
  return data.showOption
})

function handleClick() {
  data.recording = true
  data.showOption = false
}

function scrollBottom() {
  nextTick(() => {
    let wrapper = msgWrapper.value
    // console.log('wrapper.clientHeight', wrapper.clientHeight)
    // console.log('wrapper.scrollHeight', wrapper.scrollHeight)
    wrapper.scrollTo({ top: wrapper.scrollHeight - wrapper.clientHeight })
  })
}

function openRedPacket() {
  data.opening = true
  setTimeout(() => {
    data.opening = false
    nav('/message/chat/red-packet-detail')
  }, 1000)
}

async function clickItem(e) {
  if (e.type === data.MESSAGE_TYPE.RED_PACKET) {
    data.loading = true
    await _sleep(500)
    data.loading = false
    data.isOpened = e.data.state === '已过期'
    data.isShowOpenRedPacket = true
  }
}

function showTooltip(e) {
  console.log(e)
  let wrapper = null
  e.path.map((v) => {
    if (v && v.classList) {
      if (v.classList.value === 'chat-wrapper') {
        wrapper = v
      }
    }
  })
  if (wrapper) {
    // console.log(wrapper.getBoundingClientRect())
    if (wrapper.getBoundingClientRect().y - 61 > 70) {
      data.tooltipTopLocation = 'top'
      data.tooltipTop = wrapper.getBoundingClientRect().y - 70
    } else {
      data.tooltipTopLocation = 'bottom'
      data.tooltipTop =
        wrapper.getBoundingClientRect().y + wrapper.getBoundingClientRect().height + 10
    }
  }
}
</script>

<style>
.scale-enter-active,
.scale-leave-active {
  transition: transform 0.2s ease;
}

.scale-enter-from,
.scale-leave-to {
  transform: scale(0);
}
</style>
<style scoped lang="less">
@import '../../../assets/less/index';

.Chat {
  position: fixed;
  left: 0;
  right: 0;
  bottom: 0;
  top: 0;
  overflow: auto;
  color: white;
  font-size: 14rem;
  background: var(--color-message);

  .chat-content {
    > .header {
      z-index: 2;
      width: 100%;
      box-sizing: border-box;
      height: var(--common-header-height);
      padding: 0 10rem;
      display: flex;
      justify-content: space-between;
      align-items: center;
      border-bottom: 1px solid var(--line-color);

      img {
        height: 20rem;
        margin: 0 10rem;
      }

      .left {
        max-width: 60%;
        overflow: hidden;
        display: flex;
        align-items: center;

        .badge {
          margin-right: 10rem;
          font-size: 12rem;
          display: block;
          padding: 1px 6px;
          border-radius: 10px;
          color: white;
          background: var(--second-btn-color);
        }
      }

      .right {
        display: flex;
      }
    }

    .message-wrapper {
      height: calc(var(--vh, 1vh) * 100 - 125rem);
      overflow: auto;

      &.expand {
        height: calc(var(--vh, 1vh) * 100 - (125rem + var(--vh, 1vh) * 30));
      }
    }

    .footer {
      @chat-bg-color: rgb(105, 143, 244);
      @normal-bg-color: rgb(57, 57, 57);
      padding: 10rem 0;

      .toolbar {
        box-sizing: border-box;
        height: 44rem;
        margin: 0 10rem;
        padding: 5rem;
        background: @normal-bg-color;
        border-radius: 20rem;
        display: flex;
        align-items: center;

        img {
          width: 24rem;
          border-radius: 50%;
          margin-left: 15rem;
        }

        input {
          flex: 1;
          outline: none;
          border: none;
          background: @normal-bg-color;
        }

        .camera {
          margin-left: 0;
          margin-right: 5rem;
          width: 14rem;
          padding: 5rem;
          border-radius: 50%;
          background: @chat-bg-color;
        }
      }

      .record {
        box-sizing: border-box;
        height: 44rem;
        margin: 0 10rem;
        padding: 10rem 5rem;
        background: @normal-bg-color;
        border-radius: 20rem;
        display: flex;
        align-items: center;
        justify-content: center;
        position: relative;

        img {
          right: 5rem;
          position: absolute;
          width: 24rem;
          border-radius: 50%;
          margin-left: 15rem;
        }
      }

      .options {
        font-size: 14rem;
        width: 100vw;
        padding: 15rem;
        height: calc(var(--vh, 1vh) * 30);
        box-sizing: border-box;

        .option-wrapper {
          box-sizing: border-box;
          @grid-width: calc((100vw - 30rem) / 4);
          color: gray;
          display: grid;
          grid-template-columns: @grid-width @grid-width @grid-width @grid-width;

          .option {
            display: flex;
            justify-content: center;
            align-items: center;
            flex-direction: column;
            margin-bottom: 10rem;

            img {
              border-radius: 4rem;
              background: @normal-bg-color;
              padding: 10rem;
              width: 30rem;
              margin-bottom: 10rem;
            }
          }
        }
      }
    }
  }

  .preview-img {
    position: fixed;
    z-index: 9;
    top: 0;
    background: black;
    width: 100vw;
    height: calc(var(--vh, 1vh) * 100);

    .header {
      position: fixed;
      width: 100vw;
      box-sizing: border-box;
      padding: var(--page-padding);
      display: flex;
      justify-content: space-between;

      img {
        width: 22rem;
      }
    }
  }

  .tooltip {
    z-index: 9;
    left: 50%;
    margin-left: -33%;
    position: fixed;
    font-size: 12rem;
    border-radius: 6rem;
    //padding: 1rem;
    background: rgb(55, 58, 67);
    display: flex;

    .options {
      width: 45rem;
      height: 60rem;
      display: flex;
      justify-content: center;
      align-items: center;
      flex-direction: column;

      img {
        margin-bottom: 4rem;
        width: 18rem;
      }
    }

    .arrow {
      width: 0;
      height: 0;
      position: absolute;
      left: 50%;
      transform: translateX(-50%);

      &.bottom {
        top: -14rem;
        border: 7rem solid transparent;
        border-bottom: 7rem solid var(--second-btn-color);
      }

      &.top {
        bottom: -14rem;
        border: 7rem solid transparent;
        border-top: 7rem solid var(--second-btn-color);
      }
    }
  }

  .red-packet {
    z-index: 9;
    top: 0;
    position: fixed;
    width: 100vw;
    height: calc(var(--vh, 1vh) * 100);
    display: flex;
    align-items: center;
    justify-content: center;

    .content {
      width: 75vw;
      height: 55vh;
      z-index: 10;
      position: fixed;
      display: flex;
      align-items: center;
      flex-direction: column;

      .bg {
        z-index: 9;
        position: absolute;
        width: 100%;
        height: 100%;
      }

      .wrapper {
        width: 100%;
        height: 100%;
        box-sizing: border-box;
        padding: 20rem;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: space-between;
        color: #fdd9b3;
        z-index: 10;
        position: relative;

        .top {
          display: flex;
          flex-direction: column;
          align-items: center;
        }

        .avatar {
          margin-top: 60rem;
          width: 55rem;
          height: 55rem;
          border-radius: 50%;
          margin-bottom: 20rem;
        }

        .money {
          color: rgb(193, 135, 79);
          font-size: 40rem;
          font-weight: bold;
          margin-top: 15rem;
          margin-bottom: 65rem;
        }

        .belong {
          font-size: 12rem;
          margin-bottom: 30rem;
        }

        .password {
          font-size: 16rem;
        }

        .notice {
          margin-top: 150rem;
          font-size: 12rem;
        }

        .l-button {
          font-size: 16rem;
          border-radius: 0.5rem;
          margin-bottom: 30rem;
          padding: 12rem 0;
          display: flex;
          align-items: center;
          justify-content: center;
          color: rgb(120, 48, 45);
          width: 65vw;
          background: rgb(255, 217, 132);
          box-shadow: 0 0 1px;

          &.opening {
            background: rgb(228, 77, 58);

            img {
              width: 18rem;
              margin-right: 10rem;
              animation: animal 0.8s infinite linear;

              @keyframes animal {
                0% {
                  transform: rotate(-360deg);
                }
                100% {
                  transform: rotate(0deg);
                }
              }
            }
          }
        }
      }

      .close {
        bottom: -8vh;
        position: absolute;
        width: 30rem;
      }
    }
  }
}
</style>
