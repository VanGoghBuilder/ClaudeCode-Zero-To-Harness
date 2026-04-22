## Claude Code 第一次使用

安装了 Claude Code 及配置好 API 后，我们就可以开始使用了。

接下来，我们用一个最简单的示例项目来完成第一次使用。

创建一个示例项目：

~~~
mkdir runoob-claude-demo
cd runoob-claude-demo
~~~

创建一个简单文件：

~~~
touch main.py
~~~

写入以下内容：

~~~
def add(a, b):
    return a + b
~~~

#### 让 Claude Code 解释代码

在项目目录中运行：

~~~
claude
~~~

然后输入：

~~~
解释 main.py 这个文件在做什么，用新手能理解的方式说明
~~~

Claude 会读取当前目录下的代码，并给出解释。

![image](https://www.runoob.com/wp-content/uploads/2026/01/c0eea48b-3529-4d57-ab32-7a8fa40161b9.png)

#### 让 Claude Code 帮你改代码

继续在 Claude 会话中输入：

~~~
给这个函数增加类型注解，并补充基本的错误处理
~~~

Claude 会给出修改建议，通常包含：
- 修改后的代码
- 修改原因说明

你可以选择：
- 直接应用
- 手动调整
- 拒绝修改

![image](https://www.runoob.com/wp-content/uploads/2026/01/b39ec2f7-7206-4b10-a72d-292ed90fb3f8.png)

---

### Claude Code 的基本交互方式

#### 常见指令类型

你可以把对 Claude Code 的指令分为三类：

**1、解释型**

~~~
解释这段代码

这个函数为什么这么写
~~~

**2、修改型**

~~~
帮我重构这个函数

拆分成多个小函数
~~~

**3、生成型**

~~~
补一个测试用例

增加日志输出
~~~

一个简单但好用的指令模板：

~~~
在不改变现有行为的前提下，
帮我优化 XXX 文件的可读性，
并说明你做了哪些修改。
~~~

#### 常见问题：修复目录权限

如果是 Mac 或 Linux 要注意是否有权限执行，如果出现类似以下错误就是权限不够：

~~~
Error: EACCES: permission denied, open
~~~

打开终端，执行以下命令：

~~~

# 1. 修复 .claude 目录的所有权

sudo chown -R $(whoami) ~/.claude

# 2. 修复目录权限（给予读写执行权限）

chmod -R 755 ~/.claude

# 3. 确保 projects 目录可写

chmod -R 755 ~/.claude/projects
~~~

验证修复：

~~~

# 检查权限

ls -la ~/.claude/
~~~

---

### VS Code 中使用 Claude Code

如果不喜欢使用 Claude Code 的命令行模型，我们可以在 VS Code 编辑器中安装 Claude Code。

打开 VS Code，进入扩展市场，搜索 **Claude Code** 安装：

![image](https://www.runoob.com/wp-content/uploads/2025/12/cc-runoob-1.png)

安装完成后，点击右上角 Claude Code 图标，即可进入 Claude Code 页面：

![image](https://www.runoob.com/wp-content/uploads/2026/01/5c78e2a4-f9f4-4e38-91fa-d09ae892b9a4.png)

接下来就可以在对话框中使用了:

![image](https://www.runoob.com/wp-content/uploads/2026/01/22ba1d61-4b00-4d5c-ac99-d0831a6f6eeb.png)

