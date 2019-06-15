<template>
  <div>
    <editor v-model="html" :mobile-filter="true"></editor>
  </div>
</template>

<script>
import Editor from "./Editor";

export default {
  name: "app",
  components: {
    Editor
  },
  data(){
    return {
      html: ""
    }
  },
  mounted() {
    this.html = "<div>2333</div>";
  },
  methods: {}
};
</script>

<style></style>
