<template>
    <div class="index">
        <Menu mode="horizontal" theme="dark" active-name="1" ref="menu">
        <MenuItem name="1">
            <Icon type="ios-paper" />
            Json编辑器
        </MenuItem>
        <MenuItem name="2">
            <Icon type="ios-people" />
            用户管理
        </MenuItem>
        <Submenu name="3">
            <template slot="title">
                <Icon type="ios-stats" />
                统计分析
            </template>
            <MenuGroup title="使用">
                <MenuItem name="3-1">新增和启动</MenuItem>
                <MenuItem name="3-2">活跃分析</MenuItem>
                <MenuItem name="3-3">时段分析</MenuItem>
            </MenuGroup>
            <MenuGroup title="留存">
                <MenuItem name="3-4">用户留存</MenuItem>
                <MenuItem name="3-5">流失用户</MenuItem>
            </MenuGroup>
        </Submenu>
        <MenuItem name="4">
            <Icon type="ios-construct" />
            综合设置
        </MenuItem>
    </Menu>

        <!-- <link href="./css/jsoneditor.css" rel="stylesheet" type="text/css"> -->
        <!-- <link href="./css/darktheme.css" rel="stylesheet" type="text/css"> -->
        <!-- <script src="./js/jsoneditor.js"></script> -->
<!-- align="middle" -->
        <Row type="flex" justify="center" >
            <!--<Col span="24">-->
                <!--<h1>-->
                    <!--<img src="../images/logo.png">-->
                <!--</h1>-->
                <!--<h2>-->
                    <!--<p>Welcome to your iView app!</p>-->
                    <!--<Button @click="handleStart">Start iView</Button>-->
                <!--</h2>-->
            <!--</Col>-->
            <Col>
            <div id="jsonCodeEditor"></div>
            </Col>
            <Col>
            <div id="jsonTreeEditor"></div>
            </Col>
        </Row>
    </div>
</template>

<style scoped>
@import "../styles/jsoneditor.css";
@import "../styles/darktheme.css";
</style>


<style scoped lang="less">
.index {
  width: 100%;
  position: absolute;
  top: 0;
  bottom: 0;
  left: 0;
  text-align: center;
  h1 {
    height: 150px;
    img {
      height: 100%;
    }
  }
  h2 {
    color: #666;
    margin-bottom: 200px;
    p {
      margin: 0 0 50px;
    }
  }
  .ivu-row-flex {
    height: 100%;
  }
}
</style>


<script>
export default {
  data() {
    return {
      //   consts,
      activeName: "",
      openNames: []
    };
  },
  mounted: function() {
    console.log("---------------created--------------");
    this.initEditor();
  },
  methods: {
    loadScript(url, callback) {
      var script = document.createElement("script");
      script.type = "text/javascript";

      if (script.readyState) {
        //IE
        script.onreadystatechange = function() {
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
        script.onload = function() {
          callback();
        };
      }
      script.src = url;
      document.getElementsByTagName("head")[0].appendChild(script);
    },
    getWinWidth() {
      var winWidth = 0;

      if (window.innerWidth) winWidth = window.innerWidth;
      else if (document.body && document.body.clientWidth)
        winWidth = document.body.clientWidth;

      if (document.documentElement && document.documentElement.clientWidth)
        winWidth = document.documentElement.clientWidth;

      return winWidth;
    },
    getWinHeight() {
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
    editorSize() {
      var winWidth = this.getWinWidth();
      var winHeight = this.getWinHeight();

      //   var nav = document.getElementById("nav");
      //   var navHeight = nav.clientHeight || nav.offsetHeight;
      console.log("-------->", this.$refs.menu);
      var menuHeight = this.$refs.menu.$el.clientHeight;
      //   var menuHeight = this.$refs.menu.clientHeight;
      var editorHeight = winHeight - menuHeight - 2;
      var editorWidth = winWidth / 2;

      //   var menuHeight = window.getComputedStyle(this.$refs.menu).height;

      console.log("winHeight", winHeight);
      console.log("menuHeight", menuHeight);
      console.log("editorHeight", editorHeight);
      console.log("editorWidth", editorWidth);

      document.getElementById("jsonCodeEditor").style.height =
        editorHeight + "px";
      document.getElementById("jsonTreeEditor").style.height =
        editorHeight + "px";

      document.getElementById("jsonCodeEditor").style.width =
        editorWidth + "px";
      document.getElementById("jsonTreeEditor").style.width =
        editorWidth + "px";
    },
    initEditor() {
      console.log("---------------initEditor--------------");
      this.loadScript("/dist/jsoneditor.min.js", this.initEditorCallback);
    },
    initEditorCallback() {
      // create code editor
      var jsonCodeEditor = new JSONEditor(
        document.getElementById("jsonCodeEditor"),
        {
          modes: ["code"],
          onChangeText: function(jsonString) {
            jsonTreeEditor.updateText(jsonString);
          }
        }
      );

      // create tree editor
      var jsonTreeEditor = new JSONEditor(
        document.getElementById("jsonTreeEditor"),
        {
          onChangeText: function(jsonString) {
            jsonCodeEditor.updateText(jsonString);
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
        object: { a: "b", c: "d" },
        string: "Hello World"
      };
      jsonCodeEditor.set(json);
      jsonTreeEditor.set(json);
    }
  }
};
</script>
