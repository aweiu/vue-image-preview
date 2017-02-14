<template xmlns:v-el="http://www.w3.org/1999/xhtml">
  <image-preview-el-wrap>
    <slot></slot>
    <image-preview-el
      :style="{left: left + 'px', top: top + 'px', width: width + 'px', height: height + 'px', backgroundImage: 'url(' + dataUrl + ')'}"
      v-show="isShow">
      <image-preview-el-remove @click="remove(true)">×</image-preview-el-remove>
    </image-preview-el>
  </image-preview-el-wrap>
</template>
<style scoped>
  image-preview-el-wrap {
    position: relative;
  }

  image-preview-el-wrap image-preview-el {
    position: absolute;
    background: no-repeat center;
    background-size: contain;
  }

  image-preview-el-wrap image-preview-el img {
    position: absolute;
    max-height: 100%;
    max-width: 100%;
  }

  image-preview-el-wrap image-preview-el image-preview-el-remove {
    display: block;
    height: 14px;
    width: 14px;
    line-height: 14px;
    text-align: center;
    background-color: #e8464a;
    color: white;
    border-radius: 50%;
    position: absolute;
    top: -4px;
    right: -4px;
    cursor: pointer;
  }
</style>
<script>
  // 手动注册自定义标签来消灭Unknown custom element错误警告
  for (let tagName of ['image-preview-el-wrap', 'image-preview-el', 'image-preview-el-remove']) {
    try {
      document.registerElement(tagName)
    } catch (registerErr) {
    }
  }

  function getOffset (n1, n2) {
    var nr1 = n1.getBoundingClientRect()
    var nr2 = n2.getBoundingClientRect()
    return {
      left: nr1.left - nr2.left,
      top: nr1.top - nr2.top
    }
  }

  export default {
    data () {
      return {
        dataUrl: '',
        top: 0,
        left: 0,
        height: 0,
        width: 0,
        isShow: false
      }
    },
    props: ['size', 'fileSize'],
    methods: {
      preview (file, target) {
        var self = this
        var reader = new window.FileReader()
        reader.readAsDataURL(file)
        reader.onload = function () {
          self.dataUrl = this.result
          if (self.size) {
            var img = this._img || (this._img = document.createElement('img'))
            img.src = this.result
            img.onload = function () {
              var size = self.size
              if (this.width !== size[0] || this.height !== size[1]) {
                self.remove()
                self.$dispatch('onImagePreviewError', '请使用尺寸为' + size[0] + '*' + size[1] + '的图片')
              } else self.showImg(target)
            }
          } else self.showImg(target)
        }
      },
      remove (isDispatch) {
        if (isDispatch && this.dataUrl !== '') {
          this.$dispatch('onImagePreviewRemove', {
            file: this.fileInput.files[0],
            dataUrl: this.dataUrl,
            imagePreview: this
          })
        }
        this.fileInput.file = null
        this.fileInput.value = ''
        this.isShow = false
        this.dataUrl = ''
      },
      showImg (target) {
        var offset = getOffset(target, this.$el)
        var rect = target.getBoundingClientRect()
        this.top = offset.top
        this.left = offset.left
        this.width = rect.width
        this.height = rect.height
        this.isShow = true
        this.$dispatch('onImagePreview', {
          file: this.fileInput.files[0],
          dataUrl: this.dataUrl,
          imagePreview: this
        })
      }
    },
    ready () {
      var self = this
      var label = this.$el.firstElementChild
      var input = this.fileInput || (this.fileInput = label.control)
      input.addEventListener('change', function () {
        var file = this.files[0]
        var fileSize = self.fileSize / 1
        if (fileSize && file.size > fileSize) {
          var size = ['B', 'KB', 'MB']
          for (var i = 2; i > -1; i--) {
            var formatSize = (fileSize / Math.pow(1024, i))
            if (formatSize >= 1) break
          }
          self.remove()
          return self.$dispatch('onImagePreviewError', '图片最大不能超过' + formatSize + size[i])
        }
        if (this.accept.trim().toLowerCase().split(',').indexOf(file.type) === -1) {
          self.remove()
          return self.$dispatch('onImagePreviewError', '图片格式不正确')
        }
        self.preview(file, label)
      })
    }
  }
</script>
