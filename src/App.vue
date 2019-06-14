<template>
  <div ref="container"></div>
</template>

<script>

// https://www.kancloud.cn/wangfupeng/wangeditor3/332599
export default {
  name: "app",
  mounted() {
    this.editor = new window.wangEditor(this.$refs.container);
    this.editor.customConfig.uploadImgServer = "/upload";
    this.editor.customConfig.uploadImgMaxSize = 5 * 1024 * 1024; // 5m
    this.editor.customConfig.uploadImgParams = {
      // 如果版本 <=v3.1.0 ，属性值会自动进行 encode ，此处无需 encode
      // 如果版本 >=v3.1.1 ，属性值不会自动 encode ，如有需要自己手动 encode
      token: "abcdef12345"
    };
    this.editor.customConfig.uploadImgParamsWithUrl = true;
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
      return content + "<p>在粘贴内容后面追加一行</p>";
    };
    this.editor.create();
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

<style></style>
