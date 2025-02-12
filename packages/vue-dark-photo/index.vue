<!--
 * @Author: DarkLai
 * @Date: 2020-11-13 17:45:57
 * @Description : VDPhoto 组件
-->
<template>
  <div>
    <el-dialog
      :visible="showBox"
      @close="destroy"
      v-bind="$attrs"
      v-on="$listeners"
    >
      <div slot="title" class="title">{{ title }}</div>
      <section class="header-photo" slot="footer">
        <div class="head-content">
          <div class="tools-wrap">
            <div class="photo-tools">
              <el-tooltip effect="dark" content="缩小">
                <span @click="narrow" class="el-icon-zoom-out"></span>
              </el-tooltip>
              <el-tooltip effect="dark" content="实际大小">
                <span @click="reduction" class="el-icon-view"></span>
              </el-tooltip>
              <el-tooltip effect="dark" content="放大">
                <span @click="enlarge" class="el-icon-zoom-in"></span>
              </el-tooltip>
              <el-tooltip effect="dark" content="旋转">
                <span @click="rotate" class="el-icon-refresh"></span>
              </el-tooltip>
              <el-tooltip effect="dark" content="下载">
                <span
                  @click="downloadFile(imgData)"
                  class="el-icon-download"
                ></span>
              </el-tooltip>
              <el-tooltip effect="dark" content="打印">
                <span class="el-icon-printer" @click="publish"> </span>
              </el-tooltip>
            </div>
          </div>
        </div>
      </section>

      <section class="content">
        <!--startprint-->
        <img
          class="img"
          alt=""
          v-if="isImg"
          ref="imgBox"
          @mousedown="down"
          :src="imgData"
          :style="{
            transform: `translateX(${activeImg.x + 'px'}) translateY(${
              activeImg.y + 'px'
            }) scale(${activeImg.scale}) rotate(${activeImg.rotate}deg)`,
          }"
        />
        <iframe
          v-else
          :src="wordUrl"
          title=""
          class="iframe"
          ref="imgBox"
          @mousedown="down"
          :style="{
            transform: `translateX(${activeImg.x + 'px'}) translateY(${
              activeImg.y + 'px'
            }) scale(${activeImg.scale}) rotate(${activeImg.rotate}deg)`,
          }"
        ></iframe>
        <slot></slot>
        <!--endprint-->
      </section>
    </el-dialog>
  </div>
</template>

<script>
import { downloadFileByURL } from "./utils/download";
import { Tooltip, Dialog } from "element-ui";
import print from "./utils/print";
export default {
  name: "VDPhoto",
  components: {
    "el-tooltip": Tooltip,
    "el-dialog": Dialog
  },
  props: {
    // 图片数据
    imgData: {
      type: String,
      default: "",
    },
    // 图片名
    imgName: {
      type: String,
      default: "",
    },
    // 标题
    title: {
      type: String,
      default: "",
    }
  },
  data() {
    return {
      showBox: false,
      // 当前图片变换参数
      activeImg: {
        scale: 1,
        x: 0,
        y: 0,
        rotate: 0,
      },
      // word url
      wordUrl: "",
    };
  },
  methods: {
    // 初始化
    show() {
      this.showBox = true;
    },
    destroy() {
      this.showBox = false;
    },
    // 鼠标按下
    down(e) {
      let downX = e.pageX;

      let downY = e.pageY;

      let prevDix = this.activeImg.x;

      let prevDiy = this.activeImg.y;

      window.onmousemove = (a) => {
        let moveX = a.pageX;
        let moveY = a.pageY;

        let diX = parseInt(moveX - downX);

        let diY = parseInt(moveY - downY);

        this.activeImg.x = diX + prevDix;

        this.activeImg.y = diY + prevDiy;

        return false;
      };

      window.onmouseup = () => {
        window.onmousemove = window.onmouseup = null;
      };

      return false;
    },
    // 放大
    enlarge() {
      let scale = this.activeImg.scale;

      scale += 0.1;

      if (scale >= 5) {
        scale = 5;
      }

      this.activeImg.scale = scale;
    },
    // 缩小
    narrow() {
      let scale = this.activeImg.scale;

      scale -= 0.1;

      if (scale <= 0.1) {
        scale = 0.1;
      }

      this.activeImg.scale = scale;
    },
    // 还原
    reduction() {
      this.activeImg.scale = 1;
      this.activeImg.x = 0;
      this.activeImg.y = 0;
      this.activeImg.rotate = 0;
    },
    // 旋转
    rotate() {
      let rotate = this.activeImg.rotate;

      rotate += 90;

      this.activeImg.rotate = rotate;
    },
    // 下载
    downloadFile(url) {
      let name;
      if (!this.imgName) {
        let tmp = url.lastIndexOf("/");
        let tmpO = url.lastIndexOf(".");
        let tmpName = url.substring(tmp + 1, tmpO);
        name = tmpName;
      } else {
        name = this.imgName;
      }
      // 下载图片
      downloadFileByURL(url, name);
    },
    // 打印
    publish() {
      if (this.isImg) {
        print(this.$refs.imgBox);
      } else {
        console.log("请下载后打印");
      }
      this.$emit("publish", this.$refs.imgBox);
    },
  },
  computed: {
    isImg() {
      try {
        // 截取文件类型
        let idx = this.imgData.lastIndexOf(".");
        let tmpName = this.imgData.substring(idx + 1, this.imgData.length);
        if (tmpName == "png" || tmpName == "jpg") {
          return true;
        } else {
          // 调用微软api 实现 在线预览 word文档
          if (tmpName == "pdf") {
            // pdf 无需调用
            this.wordUrl = this.imgData;
          } else {
            this.wordUrl = `https://view.officeapps.live.com/op/view.aspx?src=${this.imgData}`;
          }
          return false;
        }
      } catch (error) {
        console.log("捕捉：", error);
        return false;
      }
    },
  },
};
</script>


<style lang="less" scoped>
.title {
  text-align: center;
}
.header-photo {
  width: 100%;
  background: #fff;
  .head-content {
    height: 43px;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  // 工具条
  .tools-wrap {
    .base-tools {
      height: inherit;
      padding: 0 22px;

      & > span {
        font-size: 10px;
        cursor: pointer;
        -webkit-app-region: no-drag;

        &:not(:nth-of-type(1)) {
          margin-left: 30px;
        }
      }
    }

    .photo-tools {
      height: inherit;
      padding: 0 22px;

      & > span {
        font-size: 20px;
        cursor: pointer;
        -webkit-app-region: no-drag;

        &:not(:nth-of-type(1)) {
          margin-left: 24px;
        }
      }
    }
  }
}

.content {
  overflow: hidden;
  text-align: center;
  .img {
    max-height: 100%;
    width: 100%;
    margin: auto;
  }
  .iframe {
    width: 100%;
    min-height: 500px;
  }
}
</style>
<style media="print">
@page {
    size: auto;  /* auto is the initial value */
    margin: 0mm; /* this affects the margin in the printer settings */
}
</style>
