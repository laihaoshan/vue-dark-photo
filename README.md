# 先上效果图

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f5c17fd190f742c8adbe768f3f58d454~tplv-k3u1fbpfcp-watermark.image)

# vue-dark-photo

- 基于 element-ui2.x 开发的预览图片组件
- 支持 放大、缩小、复原、下载、打印等功能
- 支持在线预览pdf、word等文档
- 暂时只支持jpg、png格式的图片
- 继承 el-dialog 等组件的属性事件等
- 适合使用了element-ui的项目
- GitHub地址：https://github.com/Dark2017/vue-dark-photo.git

# 安装使用说明

```
npm i vue-dark-photo

// 在入口（main.js）中引入并挂载
import VDPhoto from 'vue-dark-photo'
// 引入vue-dark-photo 样式
import 'vue-dark-photo/lib/vue-dark-photo.css'
// 引入element 样式
import 'element-ui/lib/theme-chalk/index.css'

Vue.use(VDPhoto)


```
# 示例

## 调用全局方法打开(适合简单使用)

```
this.$VDPhoto.show({
    imgData: "url",
    imgName: '图片名',
    title: 'title',
});

```

## 组件形式打开(适合需要额外功能)
```
<VDPhoto 
    ref="VDPhoto"
    :imgData='imgData'
    :imgName='imgName'
    :title='title'
    @publish='publish'
    ....
/>

```

# api

## 属性

| 属性值 |  类型 | 描述 | 默认值 | 
| --- | --- | --- | ---
| imgData | string | 图片地址(url) | -
| imgName | string | 图片名称 | -
| title | string | 标题 | -
| width | string | 弹窗宽度 | 50%
| ··· | ··· | ···

## 事件
| 事件名 |  说明 | 回调参数
| --- | --- | ---
| publish | 打印后的回调 | 打印的内容
| ··· | ··· | ···

## 方法
| 方法名 |  说明 | 
| --- | --- | 
| show | 打开photo | 
| ··· | ··· | 

## 插槽
| 插槽名 |  说明 | 
| --- | --- | 
| - | 图片下面的内容 | 
| ··· | ··· | 


## 注意
- el-dialog的属性及方法等也可以使用具体可移至：https://element.eleme.cn/#/zh-CN/component/dialog
- 该组件中visible属性建议不要使用以免产生不必要的BUG，通过this.$refs.VDPhoto.show()来打开组件

# 最后

- 如果对你有帮助，请star一个哦，你的鼓励是我创作的动力
- 欢迎来到我的博客，希望能对你有所帮助
- 掘金：https://juejin.cn/user/2339399368751325/posts
- csdn：https://blog.csdn.net/weixin_44083712?spm=1010.2135.3001.5343
- csdn | 掘金 | 知乎 同名： 饼干_  或  饼干 


