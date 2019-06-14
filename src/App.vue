<template>
  <div id="app">
    <div ref="container"></div>
  </div>
</template>

<script>
export default {
  name: "app",
  mounted() {
    const editor = new window.wangEditor(this.$refs.container);
    editor.customConfig.uploadImgServer = "/upload";
    editor.customConfig.uploadImgMaxSize = 5 * 1024 * 1024; // 5m
    editor.customConfig.uploadImgParams = {
      // 如果版本 <=v3.1.0 ，属性值会自动进行 encode ，此处无需 encode
      // 如果版本 >=v3.1.1 ，属性值不会自动 encode ，如有需要自己手动 encode
      token: "abcdef12345"
    };
    editor.customConfig.uploadImgParamsWithUrl = true;
    editor.customConfig.uploadFileName = "file";
    editor.customConfig.uploadImgHooks = {
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
    editor.create();
  }
};
</script>

<style></style>
