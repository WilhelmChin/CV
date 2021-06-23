<div>
    <!-- 递增动画的代码 -->
    <div id="app" class="styleEditor" ref="scroller">
      <!-- 预格式化的文本。 被包围在 <pre> 标签 元素中的文本通常会保留空格和换行符 -->
      <pre>
      {{ code }}
      </pre>
    </div>
    <ResumeEditor
      ref="resumeEditor"
      :markdown="currentMarkdown"
      :enableHtml="enableHtml"
    ></ResumeEditor>
    <!-- style样式 -->
    <div v-html="styleCode"></div>
  </div>