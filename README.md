# vue-image-preview
本地图片预览组件

## 安装
```
npm install vue-image-preview
```
## 使用
**VUE版本：1.x** <br>
**必须在vue-cli生成的webpack模板环境中使用**<br>
代码片段：
```
<template>
  <image-preview :image-size="[640, 300]" image-file-size="1048576">
    <label>
      <input type="file" accept="image/jpeg,image/png,image/gif">
    </label>
  </image-preview>
</template>
<!--需要自定义图片上传控件的样式-->
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
## Props
### imageSize(Array[Int, Int])
用于限制选择图片的像素尺寸

### imageFileSize(Number/StringNumber)
用于限制选择图片的文件大小

## Events
### onImagePreview
图片预览成功的事件。事件参数Obj：
```
{
  file: 当前图片的File对象,
  dataUrl: 当前图片的DataUrl,
  imagePreview: 当前的vueImagePreview组件实例
}
```
### onImagePreviewError
图片预览失败的事件。事件参数errMsg可能为如下值：<br>
* 请使用尺寸为{width}\*{height}的图片
* 图片最大不能超过{imageFileSize}(MB/KB/B)
* 图片格式不正确

## 效果展示
*图片上传控件的具体样式需要你自定义*
![image](https://github.com/aweiu/vue-image-preview/master/example.png)

