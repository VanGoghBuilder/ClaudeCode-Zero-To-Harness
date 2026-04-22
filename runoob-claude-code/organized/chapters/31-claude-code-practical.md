## Claude Code 实战

本章节我们将介绍如何用 Claude Code 通过我们的描述提示词开发华尔街日报风格新闻卡片应用。

整个过程尽量不写一行代码，用我们的自然语言描述让 AI 帮我们完成整个项目。

**目标 **: 使用 React + Tailwind CSS 创建一个华尔街日报风格的新闻卡片生成器**功能特性**:
- 经典的 WSJ 设计风格（衬线字体、简洁排版）
- 新闻卡片生成（标题、摘要、作者、时间）
- 支持图片上传和预览
- 导出为图片功能
- 响应式设计
- 实时预览编辑

**技术栈**:
- React 18
- Tailwind CSS 3
- Vite (构建工具)
- html2canvas (导出图片)

---

### 完整开发流程

#### 第一阶段：项目初始化）

##### Step 1: 创建项目结构

打开终端，启动 Claude Code：

~~~
cd ~/projects
claude
~~~

在 Claude Code 中输入：

~~~
我要创建一个新的 React 项目，使用 Vite 作为构建工具
项目名称：wsj-news-card-generator
要求：
1. 使用 Vite 创建 React 项目
2. 集成 Tailwind CSS
3. 添加必要的依赖：html2canvas（用于导出图片）
4. 创建清晰的文件夹结构

请给我完整的命令行步骤
~~~

Claude 会返回类似如下的内容：

![image](https://www.runoob.com/wp-content/uploads/2026/01/4d1b3ddb-23ff-49cb-b409-46689691e309.png)

Claude Code 给出了完整的操作步骤，我们可以按它给的内容完成项目创建，然后按以下步骤一步步创建文件，慢慢调整。

另外你也可以在提示词设置让它自动创建，要确保有可执行权限。

自动创建的过程，有一些提示，选择 yes，回车即可：

![image](https://www.runoob.com/wp-content/uploads/2026/01/e2adba9c-7742-473d-a66d-77616aa4a9ff-1.png)

完成后，生成了完整的项目结构，如下所示：

![image](https://www.runoob.com/wp-content/uploads/2026/01/e574d5bc-2c82-475b-bf42-2aed5fd3219b.png)

然后，继续在 Claude Code 中：

~~~
现在帮我配置 Tailwind CSS
修改 tailwind.config.js，添加自定义配置：
1. 添加 WSJ 风格的颜色（深灰色文字、金色强调色）
2. 添加衬线字体（Playfair Display, Merriweather）
3. 扩展阴影效果
~~~

![image](https://www.runoob.com/wp-content/uploads/2026/01/6558ea70-56fc-4c82-9767-ef7a74ab7015.png)

Claude 会生成 tailwind.config.js：

![image](https://www.runoob.com/wp-content/uploads/2026/01/cf5a37a8-fd14-4422-82df-28ec36554118.png)

继续配置 CSS：

~~~
修改 src/index.css，导入 Tailwind 和字体
~~~

![image](https://www.runoob.com/wp-content/uploads/2026/01/6852948d-ae64-4b2c-bec2-857a3c3fce0f.png)

#### 第二阶段：创建核心组件

##### Step 2: 创建项目文件结构

~~~
创建以下文件夹结构：
src/
  components/
    NewsCard.jsx          # 新闻卡片组件
    NewsCardEditor.jsx    # 编辑器组件
    ExportButton.jsx      # 导出按钮组件
  utils/
    exportImage.js        # 导出图片工具函数
  App.jsx
  main.jsx
~~~

执行过程，一路 yes：

![image](https://www.runoob.com/wp-content/uploads/2026/01/a9c51dee-f0d9-4260-8ffa-a502e49b1c03.png)

##### Step 3: 开发 NewsCard 组件

~~~
创建 src/components/NewsCard.jsx
要求：
1. 华尔街日报经典风格的新闻卡片
2. 包含：大标题、副标题、正文摘要、作者、日期、分类标签
3. 可选的特色图片（如果提供）
4. 使用 Tailwind 类名，体现 WSJ 的排版美学
5. 添加金色装饰线条
6. 卡片尺寸：适合社交媒体分享（1200x630px）
~~~

执行过程，一路 yes：

![image](https://www.runoob.com/wp-content/uploads/2026/01/294cb0f9-641d-4e2f-b846-06b9fd326b76.png)

Claude 生成 NewsCard.jsx：

![image](https://www.runoob.com/wp-content/uploads/2026/01/2f9109e0-849e-48b8-bb63-32c5b082c4fd.png)

##### Step 4: 创建编辑器组件

~~~
创建 src/components/NewsCardEditor.jsx
要求：
1. 左侧是表单编辑区域
2. 右侧是实时预览区域
3. 表单包含：分类、标题、副标题、摘要、作者、日期、图片上传
4. 使用受控组件
5. 图片上传支持拖拽和点击
6. 优雅的表单设计，符合 WSJ 风格
~~~

![image](https://www.runoob.com/wp-content/uploads/2026/01/69df7389-a58c-46bc-bebc-b289f130d535.png)

##### Step 5: 创建导出功能

~~~
创建 src/components/ExportButton.jsx
要求：
1. 使用 html2canvas 导出卡片为 PNG
2. 显示加载状态
3. 点击后自动下载
4. 优雅的按钮设计
~~~

![image](https://www.runoob.com/wp-content/uploads/2026/01/75c65c6d-6ea5-4b47-a8a6-0f8e88edf307.png)

#### 第三阶段：优化与增强

##### Step 7: 添加预设模板功能

~~~
在 NewsCardEditor 组件中添加预设模板功能
要求：
1. 创建 3-5 个不同主题的新闻模板
2. 用户可以快速选择模板
3. 选择后自动填充表单
4. 在编辑器上方显示模板选择器
~~~

![image](https://www.runoob.com/wp-content/uploads/2026/01/b03faf41-f6c8-4422-91dd-07b7b51648be.png)

完整效果：

![image](https://www.runoob.com/wp-content/uploads/2026/01/2fe56021-dfa7-456a-adbe-a34a9acd38bd.png)

让它改成适合中文信息的内容

~~~
所有描述改为中文，适合生成中文信息的新闻
~~~

完整效果：

![image](https://www.runoob.com/wp-content/uploads/2026/01/be5bd04b-2052-47f2-b090-8c50c018c3fa-scaled.png)

