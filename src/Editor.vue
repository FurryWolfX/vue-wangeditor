<template>
  <div ref="container"></div>
</template>

<script>
// https://www.kancloud.cn/wangfupeng/wangeditor3/332599
function parseDom(str) {
  const ele = document.createElement("div");
  ele.innerHTML = str;
  return ele.childNodes;
}
export default {
  name: "Editor",
  props: ["value", "mobileFilter"],
  mounted() {
    this.editor = new window.wangEditor(this.$refs.container);
    this.editor.customConfig.uploadImgServer = "/upload?token=123";
    this.editor.customConfig.uploadImgMaxSize = 5 * 1024 * 1024; // 5m
    this.editor.customConfig.onchange = html => {
      // html 即变化之后的内容
      this.$emit("input", html);
    };
    this.editor.customConfig.uploadFileName = "file";
    this.editor.customConfig.uploadImgHooks = {
      before: function(xhr, editor, files) {},
      success: function(xhr, editor, result) {},
      fail: function(xhr, editor, result) {},
      error: function(xhr, editor) {},
      timeout: function(xhr, editor) {},
      customInsert: function(insertImg, result, editor) {
        const url = result.url;
        insertImg(url);
      }
    };
    // 自定义处理粘贴的文本内容
    this.editor.customConfig.pasteTextHandle = function(content) {
      // content 即粘贴过来的内容（html 或 纯文本），可进行自定义处理然后返回
      if (this.mobileFilter) {
        // 过滤样式适配手机
        const dom = parseDom("<div class='paste'>" + content + "</div>");
        const imageList = dom[0].querySelectorAll("img");
        imageList.forEach(image => {
          // 去掉图片的固定宽度，改为 100%
          const w = image.width || image.style.width;
          const h = image.height || image.style.height;
          if (w) {
            image.setAttribute("o-width", w);
            image.setAttribute("o-height", h);
            image.setAttribute("width", "100%");
            image.removeAttribute("height");
          }
        });
        return dom[0].outerHTML;
      } else {
        return content;
      }
    };
    this.editor.create();
  },
  watch: {
    value: {
      handler(newValue) {
        this.setContent(newValue);
      },
      immediate: false
    }
  },
  methods: {
    setContent(html) {
      this.editor.txt.html(html);
    },
    getContent() {
      this.editor.txt.html();
    }
  }
};
</script>

<style scoped></style>
