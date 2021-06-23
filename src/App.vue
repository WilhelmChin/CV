<template>
  <div id="app">
    <StyleEditor ref="styleEditor" :code="currentStyle"></StyleEditor>
    <ResumeEditor
      ref="resumeEditor"
      :markdown="currentMarkdown"
      :enableHtml="enableHtml"
    ></ResumeEditor>
  </div>
</template>

<script>
import StyleEditor from "./components/StyleEditor";
import ResumeEditor from "./components/ResumeEditor";
import "./assets/reset.css";

export default {
  name: "app",
  components: {
    StyleEditor,
    ResumeEditor,
  },
  data() {
    return {
      interval: 1,
      currentStyle: "",
      enableHtml: false,
      fullStyle: [
        `/*
* Inspired by https://juejin.im/post/5cad69dde51d456e4c4bffd
* Hi, this is WinterChen
* 这是我的简历！
*/

/* 首先让所有元素有过渡效果 */
* {
  transition: all .3s;
}
/* 给背景来点花样 */
html {
  color: #fff200; background: #192a56;
}
/* 文字离边框太近 */
.styleEditor {
  padding: .5em;
  border: 1px solid;
  margin: .5em;
  font-size:1.5em;
  overflow: auto;
  width: 45vw; height: 100vh;
}
/* 代码高亮 */
.token.selector{ color: #fbc531; }
.token.property{ color: #d2dae2; }
.token.punctuation{ color: yellow; }
.token.function{ color: #ff9f1a; }

/* 加点 3D 效果 */
html{
  perspective: 800px;
}
.styleEditor {
  position: absolute; left: 0; top: 0;
  -webkit-transition: none;
  transition: none;
  -webkit-transform:rotateY(5deg) translateZ(-100px) ;
          transform:rotateY(5deg) translateZ(-100px) ;
}

/* 准备一个编辑器 */
.resumeEditor{
  position: fixed; right: 0; top: 0;
  padding: .5em;  margin: .5em;
  width: 48vw; height: 90vh;
  border: 1px solid;
  background: white; color: #222;
  overflow: auto;
}

`,
        `
/* 格式化Markdown
 */
`,
        `
/* 再对 HTML 加点样式 */
.resumeEditor{
  padding: 2em;
  padding-top: .2em;
}
.resumeEditor h2{
  display: inline-block;
  border-bottom: 1px solid;
  margin: 1em 0 .5em;
}
.resumeEditor ul,.resumeEditor ol{
  list-style: none;
}
.resumeEditor ul> li::before{
  content: '•';
  margin-right: .5em;
}
.resumeEditor ol {
  counter-reset: section;
}
.resumeEditor ol li::before {
  counter-increment: section;
  
  margin-right: .5em;
}
.resumeEditor blockquote {
  margin: 1em;
  padding: .5em;
  background: #ddd;
}
`,
      ],
      currentMarkdown: "",
      fullMarkdown: `技能
----
* CET-6          
* HTML、CSS、JavaScript基础
* Vue、React、微信小程序、Node.js 开发

项目
----

#### React
1. <a href="https://react-mobileshop.herokuapp.com/" target="_blank">手机商城/附管理</a>&emsp;&emsp;&emsp;Redux&emsp;Express&emsp;MongoDB&emsp;Bootstrap
2. <a href="https://rocky-reef-17667.herokuapp.com/" target="_blank">React-社交平台</a>&emsp;&emsp;&emsp;Redux&emsp;koa&emsp;MongoDB&emsp;Bootstrap
<br/>
<br/>

#### Vue
3. <a href="https://aqueous-stream-06252.herokuapp.com/" target="_blank">Vue2-社交平台</a>&emsp;&emsp;&emsp;Vuex&emsp;express&emsp;MongoDB&emsp;&emsp;<strong><small>效果和React项目2一致</small></strong>
4. <a href="https://pure-shore-34580.herokuapp.com/" target="_blank">Vue3-账务管理</a>&emsp;&emsp;&emsp;Vuex&emsp;express&emsp;MongoDB&emsp;element-ui
5. <a href="https://peaceful-fortress-68169.herokuapp.com/" target="_blank">Vue3-后台管理</a>&emsp;&emsp;&emsp;TypeScript&emsp;express&emsp;MongoDB&emsp;element-ui&emsp;Echarts
6. <a href="https://shielded-fjord-11129.herokuapp.com" target="_blank">Vue2-微信聊天</a>&emsp;&emsp;&emsp;Vuex&emsp;websocket&emsp;express&emsp;MongoDB
7. <a href="https://hidden-headland-89198.herokuapp.com/" target="_blank">Vue2-朋友圈</a>&emsp;&emsp;&emsp;&emsp;express&emsp;MongoDB
<br/>
<br/>

#### 小程序
8. 仿京东购物&emsp;&emsp;&emsp;&emsp;原生小程序
9. 云在线课堂&emsp;&emsp;&emsp;&emsp;mpvue
<br/>
<br/>

#### **Vue & React 项目因使用 heroku 免费服务器，首次加载请稍等，小程序因涉及支付，审核未通过，可当面展示，感谢理解*
<br/>

> &emsp;作者: **陈方皓**&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;微信/手机: **1572 6816 384**  <br/>
<br/>
&emsp;邮箱: **670921096@qq.com**
`,
    };
  }, //data结束
  created() {
    this.makeResume();
  },

  methods: {
    makeResume: async function () {
      await this.progressivelyShowStyle(0);
      await this.progressivelyShowResume();
      await this.progressivelyShowStyle(1);
      await this.showHtml();
      await this.progressivelyShowStyle(2);
    },
    showHtml: function () {
      return new Promise((resolve, reject) => {
        this.enableHtml = true;
        resolve();
      });
    },
    progressivelyShowStyle(n) {
      return new Promise((resolve, reject) => {
        let interval = this.interval;
        let showStyle = async function () {
          let style = this.fullStyle[n];
          if (!style) {
            return;
          }
          // 计算前 n 个 style 的字符总数
          let length = this.fullStyle
            .filter((_, index) => index <= n)
            .map((item) => item.length)
            .reduce((p, c) => p + c, 0);
          let prefixLength = length - style.length;
          if (this.currentStyle.length < length) {
            let l = this.currentStyle.length - prefixLength;
            let char = style.substring(l, l + 1) || " ";
            this.currentStyle += char;
            if (style.substring(l - 1, l) === "\n" && this.$refs.styleEditor) {
              this.$nextTick(() => {
                this.$refs.styleEditor.goBottom();
              });
            }
            setTimeout(showStyle, interval);
          } else {
            resolve();
          }
        }.bind(this);
        showStyle();
      });
    },
    progressivelyShowResume() {
      return new Promise((resolve, reject) => {
        let length = this.fullMarkdown.length;
        let interval = this.interval;
        let showResume = () => {
          if (this.currentMarkdown.length < length) {
            this.currentMarkdown = this.fullMarkdown.substring(
              0,
              this.currentMarkdown.length + 1
            );
            let lastChar =
              this.currentMarkdown[this.currentMarkdown.length - 1];
            let prevChar =
              this.currentMarkdown[this.currentMarkdown.length - 2];
            if (prevChar === "\n" && this.$refs.resumeEditor) {
              this.$nextTick(() => this.$refs.resumeEditor.goBottom());
            }
            setTimeout(showResume, interval);
          } else {
            resolve();
          }
        };
        showResume();
      });
    },
  },
};
</script>

<style scoped>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

html {
  min-height: 100vh;
}
* {
  box-sizing: border-box;
}
</style>
