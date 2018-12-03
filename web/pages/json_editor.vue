<template>
  <v-layout row justify-center align-center>
    <link href="/jsoneditor.min.css" rel="stylesheet" type="text/css">
    <link href="/darktheme.css" rel="stylesheet" type="text/css">
    <transition name="fade">
      <loading v-if="isLoading"></loading>
    </transition>
    <v-flex xs6 sm8 md6>
      <div id="jsonCodeEditor"></div>
    </v-flex>
    <v-flex xs6 sm8 md6>
      <div id="jsonTreeEditor"></div>
    </v-flex>
  </v-layout>
</template>

<style scoped>
  /*@import "/jsoneditor.css";*/
  /*@import "/darktheme.css";*/
  .fade-enter,
  .fade-leave-active {
    opacity: 0;
  }
  .fade-enter-active,
  .fade-leave-active {
    transition: opacity 0.5s;
  }
</style>

<script>
import Logo from '~/components/Logo.vue'
import VuetifyLogo from '~/components/VuetifyLogo.vue'
import Loading from '~/components/loading'

export default {
  components: {
    Logo,
    VuetifyLogo,
    Loading
  },
  data(){
    return{
      isLoading: true
    }
  },
  mounted: function() {
    // console.log("---------------created--------------");
    this.initEditor();
  },
  methods: {
    loadScript (url, callback) {
      var script = document.createElement("script");
      script.type = "text/javascript";

      if (script.readyState) {
        //IE
        script.onreadystatechange = function () {
          if (
            script.readyState == "loaded" ||
            script.readyState == "complete"
          ) {
            script.onreadystatechange = null;
            callback();
          }
        };
      } else {
        //Others
        script.onload = function () {
          callback();
        };
      }
      script.src = url;
      document.getElementsByTagName("head")[0].appendChild(script);
    },
    getWinWidth () {
      var winWidth = 0;

      if (window.innerWidth) winWidth = window.innerWidth;
      else if (document.body && document.body.clientWidth)
        winWidth = document.body.clientWidth;

      if (document.documentElement && document.documentElement.clientWidth)
        winWidth = document.documentElement.clientWidth;

      return winWidth;
    },
    getWinHeight () {
      //获取浏览器窗口高度
      var winHeight = 0;

      if (window.innerHeight) winHeight = window.innerHeight;
      else if (document.body && document.body.clientHeight)
        winHeight = document.body.clientHeight;

      //通过Document对body进行检测，获取浏览器可视化高度
      if (document.documentElement && document.documentElement.clientHeight)
        winHeight = document.documentElement.clientHeight;

      return winHeight;
    },
    editorSize () {
      var winWidth = this.getWinWidth();
      var winHeight = this.getWinHeight();

      //   var nav = document.getElementById("nav");
      //   var navHeight = nav.clientHeight || nav.offsetHeight;
      // console.log("-------->", this.$refs.menu);
      // var menuHeight = this.$refs.menu.$el.clientHeight;
      //   var menuHeight = this.$refs.menu.clientHeight;
      var editorHeight = winHeight;
      var editorWidth = winWidth / 2;
      //   var menuHeight = window.getComputedStyle(this.$refs.menu).height;

      document.getElementById("jsonCodeEditor").style.height =
        editorHeight + "px";
      document.getElementById("jsonTreeEditor").style.height =
        editorHeight + "px";

      document.getElementById("jsonCodeEditor").style.width =
        editorWidth + "px";
      document.getElementById("jsonTreeEditor").style.width =
        editorWidth + "px";
    },
    initEditor () {
      // console.log("---------------initEditor--------------");
      this.loadScript("/jsoneditor.min.js", this.initEditorCallback);
    },
    initEditorCallback () {
      // create code editor
      var jsonCodeEditor = new JSONEditor(
        document.getElementById("jsonCodeEditor"),
        {
          mode: "code",
          onChangeText: function (jsonString) {
            jsonTreeEditor.updateText(jsonString);
          }
        }
      );

      // create tree editor
      var jsonTreeEditor = new JSONEditor(
        document.getElementById("jsonTreeEditor"),
        {
          mode: "tree",
          onChangeJSON: function (json) {
            jsonCodeEditor.update(json);
          }
        }
      );

      this.editorSize();
      window.onresize = this.editorSize;

      // set initial data in both editors
      var json = {
        array: [1, 2, 3],
        boolean: true,
        null: null,
        number: 123,
        object: {a: "b", c: "d"},
        string: "Hello World"
      };
      jsonCodeEditor.set(json);
      jsonTreeEditor.set(json);

      this.isLoading = false
    }
  }
}
</script>
