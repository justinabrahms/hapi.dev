<template>
  <div class="container">
    <ApiNav @change="onChildChange" :menu="menu" />
    <div class="tutorial-markdown-window">
      <Markdown :display="display"/>
    </div>
  </div>
</template>

<script>
import Markdown from "~/components/Markdown.vue";
import ApiNav from "~/components/Navs/ApiNav.vue";

export default {
  components: {
    Markdown,
    ApiNav
  },
  head() {
    return {
      title: "API"
    };
  },
  data() {
    return {
      display: "",
      version: "18.3.1",
      menu: ""
    };
  },
  methods: {
    async onChildChange(value) {
      this.$data.version = await value;
      await this.getAPI();
      window.scrollTo(0,0);
    },
    async getAPI() {
      const options = {
        headers: {
          accept: "application/vnd.github.v3.raw+json",
          authorization: `token 706875a0a47eff85e32ff0550fa5ff44942bd416`
        }
      };

      try {
        const res = await this.$axios.$get(
          "https://api.github.com/repos/hapijs/hapi/contents/API.md?ref=v" +
            this.$data.version,
          options
        );
        let raw = await res;
        let rawString = await raw.toString();
        let finalDisplay = await rawString
          .replace(/\/>/g, "></a>")
          .replace(/-\s\[(?:.+[\n\r])+/, "");
        let finalMenu = await rawString.match(/-\s\[(?:.+[\n\r])+/).pop();
        this.$data.menu = await finalMenu;
        this.$data.display = await finalDisplay;
      } catch (err) {
        console.log(err);
      }
    }
  },
  created() {
    this.getAPI();
  }
};
</script>

<style lang="scss">
@import "../assets/styles/main.scss";
</style>