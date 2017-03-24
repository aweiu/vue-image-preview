# vue-image-preview
本地图片预览组件

## 安装
```
npm install vue-image-preview
```
## 使用
**VUE版本：1.x** <br>
**必须在vue-cli生成的webpack模板环境中使用**<br>
```
<template>
  <image-preview :size="[640, 300]" file-size="1048576" :verify="verifyImage">
    <!--根节点必须是label-->
    <label>
      <input type="file" accept="image/jpeg,image/png,image/gif">
    </label>
  </image-preview>
</template>
<!--需要自定义图片选择控件的样式-->
<style>
  label {
  }
  label input {
    display: none;
  }
</style>
<script>
  // 注册组件
  import imagePreview from 'vue-image-preview'
  export default {
    components: {
      imagePreview
    },
    methods: {
      // 自定义图片预览的校验
      verifyImage (previewObj) {
        if (previewObj.width <= previewObj.height) return '请选择长方形图片'
      }
    },
    events: {
      onImagePreview (obj) {
        // 图片预览成功的事件
      },
      onImagePreviewError (errMsg) {
        // 图片预览失败的事件
      },
      onImagePreviewRemove (obj) {
        // 取消图片预览的事件
      }
    }
  }
</script>
```
![image](https://github.com/aweiu/vue-image-preview/raw/master/example.png)

*图片选择控件的具体样式需要你自定义*
## Props
### size(Array[Int, Int])
用于限制选择图片的像素尺寸

### fileSize(Number/StringNumber)
用于限制选择图片的文件大小。单位：byte

### verify(Function)
用于校验选择的图片。该函数如返回String数据，即代表校验不通过，该组件会将本次String数据作为出错提示dispatch出去，如返回Undefined(无返回数据)，则代表校验通过。具体可参考上面示例

该函数的接收参数为**PreviewObj**

## Events
### onImagePreview
图片预览成功的事件。事件参数为**PreviewObj**：

### onImagePreviewError
图片预览失败的事件。事件参数errMsg可能为如下值：<br>
* 请使用尺寸为{width}\*{height}的图片
* 图片最大不能超过{fileSize}(MB/KB/B)
* 图片格式不正确
* 自定义校验方法返回的内容

### onImagePreviewRemove
取消图片预览的事件。事件参数为**PreviewObj**：

## PreviewObj
```
{
  file: 当前图片的File对象,
  width: 当前图片的宽度,
  height: 当前图片的高度,
  dataUrl: 当前图片的DataUrl,
  imagePreview: 当前的vueImagePreview组件实例
}
```
