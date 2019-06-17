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
    props: ["value", "mobileFilter", "contentHeight"],
    mounted() {
      this.editor = new window.wangEditor(this.$refs.container);
      this.editor.customConfig.uploadImgServer = "/upload";
      this.editor.customConfig.uploadImgMaxSize = 5 * 1024 * 1024; // 5m
      // 自定义菜单配置
      this.editor.customConfig.menus = [
        // 'head',  // 标题
        'bold',  // 粗体
        'fontSize',  // 字号
        // 'fontName',  // 字体
        'italic',  // 斜体
        'underline',  // 下划线
        'strikeThrough',  // 删除线
        'foreColor',  // 文字颜色
        // 'backColor',  // 背景颜色
        'link',  // 插入链接
        'justify',  // 对齐方式
        'image',  // 插入图片
        'undo',  // 撤销
        'redo'  // 重复
      ]
      this.editor.customConfig.onchange = html => {
        // html 即变化之后的内容
        if (this.mobileFilter) {
          const dom = parseDom("<div>" + html + "</div>");
          html = this.filterMobileImage(dom);
        }
        this.$emit("input", html);
      };
      this.editor.customConfig.uploadFileName = "file";
      this.editor.customConfig.uploadImgHooks = {
        before: (xhr, editor, files) => {},
        success: (xhr, editor, result) => {},
        fail: (xhr, editor, result) => {},
        error: (xhr, editor) => {},
        timeout: (xhr, editor) => {},
        customInsert: (insertImg, result, editor) => {
          const url = window.SITE_CONFIG["resourceServer"] + result.picPath;
          insertImg(url);
        },
      };
      // 自定义处理粘贴的文本内容
      this.editor.customConfig.pasteTextHandle = content => {
        // content 即粘贴过来的内容（html 或 纯文本），可进行自定义处理然后返回
        if (this.mobileFilter) {
          // 过滤样式适配手机
          const dom = parseDom("<div class='paste'>" + content + "</div>");
          return this.filterMobileImage(dom);
        } else {
          return content;
        }
      };
      this.editor.create();
      if (this.contentHeight) {
        const container = document.querySelector(".w-e-text-container");
        container.style.height = this.contentHeight;
      }
    },
    watch: {
      value: {
        handler(newValue) {
          this.setContent(newValue);
        },
        immediate: false,
      },
    },
    methods: {
      filterMobileImage(dom) {
        const imageList = dom[0].querySelectorAll("img");
        imageList.forEach(image => {
          // 去掉图片的固定宽度，改为 100%
          image.removeAttribute("width");
          image.removeAttribute("height");
          image.style.width = "100%";
        });
        return dom[0].innerHTML;
      },
      setContent(html) {
        this.editor.txt.html(html);
      },
      getContent() {
        this.editor.txt.html();
      },
    },
  };
</script>

<style scoped></style>
