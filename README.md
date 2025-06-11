# OpenAI Codex功能测试

## 1. 项目介绍
&emsp;&emsp;当前项目是九天老师公开课《OpenAI最新Codex功能解读与深度评测》配套学习资料，用于测试Codex的相关功能。完整Codex功能介绍详见视频介绍（点此观看B站视频）：

[![点击观看视频](https://ml2022.oss-cn-hangzhou.aliyuncs.com/img/a1d3cd02fb7803f345d78330ec7e399.png)](https://www.bilibili.com/video/BV1vkJwzcEis/)

## 2. 项目说明
&emsp;&emsp;codex-debug项目其实是一个查询天气的流式HTTP MCP工具，项目功能本身非常完整，且可以直接运行。但从实际使用层面来说，还可以进一步优化项目功能，如将API-KEY写在核心代码内部，从而避免每次调用的时候都单独输入API-KEY。借助Codex，我们可以在完全不了解项目源码的情况下，对其提出需求，令其改变项目功能，并将其创建为自己单独的项目。

## 3. 项目下载与使用流程
- 下载项目
  ```bash
  git clone https://github.com/Hsail/codex-debug-test.git
  cd codex-debug-test
  ```
- 安装环境
  ```bash
  uv venv
  uv pip install -e .
  ```
- 功能测试
  ```bash
  mcp-get-weather
  ```
- 启动后效果
  <img src="https://ml2022.oss-cn-hangzhou.aliyuncs.com/img/image-20250518154159197.png" alt="image-20250518154159197" style="zoom:50%;" />

- 调用测试
  &emsp;&emsp;需要注意的是，这是一个流式HTTP的用于天气查询的MCP服务器，项目现在已经在核心代码中内置了 API-KEY `ABCTESTAPI`，启动后在 3000 端口即可发起流式 MCP 工具调用请求。例如在 Cherry Studio 中，调用流式 MCP 进行天气查询如下：
  - 先创建MCP工具
    <img src="https://ml2022.oss-cn-hangzhou.aliyuncs.com/img/image-20250518154336437.png" alt="image-20250518154336437" style="zoom:50%;" />
  - 然后进行调用测试
    <img src="https://ml2022.oss-cn-hangzhou.aliyuncs.com/img/image-20250518154442909.png" alt="image-20250518154442909" style="zoom:50%;" />
    能够看到，此时项目是可以顺利运行的。

## 4. 借助Codex进行项目功能优化
&emsp;&emsp;接下来进一步进行项目功能优化。能够发现，此时启动的时候需要人工手动输入API-KEY，并不方便，因此我们考虑借助Codex进行项目功能优化。
### 4.1 Codex核心特性介绍
<img src="https://ml2022.oss-cn-hangzhou.aliyuncs.com/img/image-20250517165411479.png" alt="image-20250517165411479" style="zoom:50%;" />
>Codex介绍主页：https://openai.com/index/introducing-codex/

&emsp;&emsp;OpenAI **Codex**（2025研究预览版）是一个基于云端的**软件工程智能体**，能够**并行处理多项编程任务**。它可以根据用户的自然语言指令自动编写代码、回答代码库相关的问题、修复代码中的错误，甚至为代码仓库**生成Pull Request提案**  。每个任务都会在云端一个**隔离的沙盒容器**中独立执行，并预先加载用户的代码仓库环境。这意味着 Codex 能读取和编辑仓库中的文件，运行测试、lint检查、类型检查等命令来验证代码  。当 Codex 完成任务后，会在沙盒环境中**提交代码更改**并提供**可验证的证据**（例如终端日志、测试输出的引用），方便用户审查每一步操作  。用户可以查看 Codex 的修改结果，要求进一步调整，或将这些更改转换为 GitHub 上的 Pull Request，亦或直接合并到本地环境 。为了使 Codex 更好地理解项目，开发者还可以在仓库中添加**AGENTS.md**文件，对项目背景、测试命令、编码规范等进行说明，帮助指导 Codex 进行更符合项目需求的操作 。

​&emsp;&emsp;Codex 背后的模型是 **codex-1**，这是 OpenAI **o3** 系列AI推理模型专门针对软件工程优化的一个版本 。codex-1 在大量真实编码任务环境中通过**强化学习**进行训练，能够生成风格接近人类且符合项目PR习惯的“干净”代码，更精确地遵循指令意图，并会反复运行测试直到通过为止  。值得注意的是，codex-1 **支持超大上下文**：在内部测试中它被验证可处理**最多约192k tokens**的上下文长度 （远超ChatGPT等先前模型的上下文长度），这使其可以加载大型代码库并理解其中的关系。总体而言，Codex 的架构融合了强大的大模型推理能力与真实开发环境的集成，使其能够像人类程序员一样在理解上下文的基础上自主编写、修改和验证代码。

​&emsp;&emsp;OpenAI Codex 的出现，为多个领域的工作流程带来变革。以下是Codex在**主要场景**中的典型应用：

- **软件开发：Codex 最直接的用途是在软件开发生命周期中担当“编码助手”。在日常开发中，Codex 可以分担重复且明确的任务**，例如重构代码、批量重命名、添加单元测试等，从而避免开发者频繁上下文切换 。它也擅长**构建新功能框架、连接组件、修复Bug，以及撰写文档** 。OpenAI 自身的工程团队已将 Codex 纳入日常工具，用于快速处理待办事项、在每日规划中让多个代理同时工作，加速开发迭代  。在早期封闭测试中，一些企业合作伙伴分享了Codex的用例：例如 **Cisco** 希望利用 Codex 加速实现创新想法并反馈改进方向 ；**Temporal** 用 Codex 来开发新特性、调试问题、编写并执行测试，用后台代理保持工程师“**心流**”不断档 ；邮件应用 **Superhuman** 则用 Codex 快速提高测试覆盖率、修复集成错误，甚至让产品经理也能借助 Codex 提交小规模代码改动（工程师只需最后代码审查） ；**Kodiak Robotics** 将 Codex 应用于自动撰写调试工具、重构庞大代码库，加速其自动驾驶代码的开发，同时把 Codex 当作参考助手，以获取对陌生代码的背景理解 。这些案例显示，在软件项目中引入 Codex 后，**小团队和个人开发者的生产力**都有望显著提升，复杂项目的迭代速度也会加快 。
- **数据分析：借助 Codex 的编程能力，数据分析工作也能受益。例如，用户可以让 Codex 根据自然语言指令生成数据处理脚本或分析代码**，Codex 会在沙盒环境中执行这些代码并返回结果。这类似于 ChatGPT 的“代码解释器”功能，但 Codex 能处理更复杂的多文件项目和更长时间运行。例如，给定一个数据集 CSV 文件，用户可以提示 Codex 清洗数据并绘制统计图表，Codex 会写出代码、运行所需的库（如Pandas、Matplotlib），直至生成图表或报告。由于 Codex **暂不支持图片输入**等前端交互 ，它在数据可视化上可能需要结合文本输出或保存结果文件供用户下载查看。不过，在复杂的数据管道、自动化报告生成等场景下，Codex 能**充当数据工程助手**：自动化执行重复的分析任务，或快速搭建分析原型，让数据科学家将精力更多放在问题洞见上而非繁杂的编码细节。
- **教育领域：Codex 也被寄望于辅助编程教育和训练**。对于编程初学者，Codex 可以扮演“智能辅导员”的角色：学生可以用自然语言提出编程问题或作业要求，Codex 将给出代码答案并逐步解释实现思路。这有助于初学者理解代码逻辑，提高学习效率 。例如，一个学生想实现一个排序算法，只需用日常语言描述需求，Codex 就能提供相应的代码并注释说明关键步骤。同时，教师可以让 Codex **批改学生提交的代码**、找出其中的错误或不佳之处，并给出改进建议，从而节省批改时间并提供个性化反馈。在更高级的学习场景，Codex 支持多语言编程，它可以将同一道题的解法用不同编程语言实现，这对学习比较语言差异、算法实现技巧都很有帮助  。总的来说，Codex 把强大的编码生成和理解能力以自然语言接口提供出来，让**非专业人士**也可以通过简单指令“让计算机编程”，这将降低学习编程的门槛，培养更多软件开发人才。

​&emsp;&emsp;综上，OpenAI 最新发布的 Codex 智能体融合了先进的大模型能力和实际软件开发流程，通过并行自主执行代码任务，展现出变革编程范式的巨大潜力。从官方声明、媒体报道到开发者社群的热烈讨论来看，Codex 有望在软件开发、数据分析、教育等领域催生新的高效工作模式。但同时，OpenAI 也强调目前 Codex 仍是研究预览版，**需要开发者审慎地审查其产出**，并与之保持协作监督的关系 。未来，随着OpenAI对Codex持续打磨（例如引入实时交互、扩展输入模态等  ），我们或许将见证更加**通用且智能的“AI工程师”**逐步走入开发者的日常工作。开发者们也正拭目以待，期待利用 Codex 构建出更丰富、更强大的软件应用 。

### 4.2 Codex关联GitHub账号
- 登录ChatGPT，选择左侧Codex：

<img src="https://ml2022.oss-cn-hangzhou.aliyuncs.com/img/image-20250517172930175.png" alt="image-20250517172930175" style="zoom:50%;" />

- 首次登录的时候提示需要绑定GitHub账号：

<img src="https://ml2022.oss-cn-hangzhou.aliyuncs.com/img/image-20250518160000279.png" alt="image-20250518160000279" style="zoom:50%;" />

绑定后即可开始使用Codex。

### 4.3 Fork项目与创建环境
​&emsp;&emsp;由于Codex和GitHub直接绑定，因此我们需要先Fork当前项目（也可以上传自己的项目），然后对项目进行功能优化。点击Fork，然后即可在自己的GitHub项目主页中看到拷贝的项目版本。
<img src="https://ml2022.oss-cn-hangzhou.aliyuncs.com/img/image-20250518160208420.png" alt="image-20250518160208420" style="zoom:50%;" />
  
​&emsp;&emsp;此时打开Codex，点击环境——创建环境，就能codex-debut-test项目了：
<img src="https://ml2022.oss-cn-hangzhou.aliyuncs.com/img/image-20250518160625627.png" alt="image-20250518160625627" style="zoom:50%;" />
然后选择codex-debug-test项目，点击创建环境：
<img src="https://ml2022.oss-cn-hangzhou.aliyuncs.com/img/image-20250518160708182.png" alt="image-20250518160708182" style="zoom:50%;" />
创建成功：
<img src="https://ml2022.oss-cn-hangzhou.aliyuncs.com/img/image-20250518160753049.png" alt="image-20250518160753049" style="zoom:50%;" />

### 4.4 进行codex-debug-test项目对话
​&emsp;&emsp;接下来回到codex主页，选择codex-debug-test项目，并简单询问项目功能（注意点击询问，而不是验证码），开启codex使用：
<img src="https://ml2022.oss-cn-hangzhou.aliyuncs.com/img/image-20250518160937965.png" alt="image-20250518160937965" style="zoom:50%;" />
此时codex会先创建一个任务，点击任务就能看到codex正在创建沙盒环境容器，并查询项目核心功能：
<img src="https://ml2022.oss-cn-hangzhou.aliyuncs.com/img/image-20250518161105364.png" alt="image-20250518161105364" style="zoom:50%;" />
在执行每个任务时，codex都会尽量遍历更多的代码和README文档，因此每个任务，哪怕是比较小的任务，都需要花费一小段时间。查询结束后回答结果如下：
<img src="https://ml2022.oss-cn-hangzhou.aliyuncs.com/img/image-20250518161215283.png" alt="image-20250518161215283" style="zoom:50%;" />
关于其他任何项目问题，也都可以直接提问。

### 4.5 借助Codex进行项目功能优化
​&emsp;&emsp;接下来我们尝试让Codex对我们的项目进行功能优化，我们希望改进的地方在于，`目前这个codex-debug-test项目启动时需要手动输入OpenWeather的API-KEY，非常麻烦，我希望将API-KEY设置放在核心代码内部，API-KEY具体内容为ABCTESTAPI，请帮我修改项目代码完成该需求，并进行相关功能尝试`。需要注意，如果希望Codex进行项目功能测试，则需要给与正确的OpenWeather API-KEY。

​&emsp;&emsp;此时Codex实际运行效果如下：
- 启动运行：
  <img src="https://ml2022.oss-cn-hangzhou.aliyuncs.com/img/image-20250518161557327.png" alt="image-20250518161557327" style="zoom:50%;" />
- 完成代码和README文档修改：
  <img src="https://ml2022.oss-cn-hangzhou.aliyuncs.com/img/image-20250518162520420.png" alt="image-20250518162520420" style="zoom:50%;" />
- 本地测试，接下来尝试将Codex修改后的代码直接copy覆盖原始项目代码，在本地进行测试：
  <img src="https://ml2022.oss-cn-hangzhou.aliyuncs.com/img/image-20250518162634493.png" alt="image-20250518162634493" style="zoom:50%;" />
  <img src="https://ml2022.oss-cn-hangzhou.aliyuncs.com/img/image-20250518162858076.png" alt="image-20250518162858076" style="zoom:50%;" />
  修改完代码后即可直接运行，此时不再需要输入API-KEY即可运行：
  <img src="https://ml2022.oss-cn-hangzhou.aliyuncs.com/img/image-20250518162939188.png" alt="image-20250518162939188" style="zoom:50%;" />
  测试可以顺利连接：
  <img src="https://ml2022.oss-cn-hangzhou.aliyuncs.com/img/image-20250518163054100.png" alt="image-20250518163054100" style="zoom:50%;" />
至此，Codex修改代码工作顺利完成。

### 4.6 Codex代码推送
​&emsp;&emsp;最后，当我们修改完代码并且本地测试无误后，即可进行代码推送，创建PR或者创建PR草案了：
<img src="https://ml2022.oss-cn-hangzhou.aliyuncs.com/img/image-20250518163213414.png" alt="image-20250518163213414" style="zoom:50%;" />
然后就能在项目主页中看到Codex编写的PR：
<img src="https://ml2022.oss-cn-hangzhou.aliyuncs.com/img/image-20250518163456941.png" alt="image-20250518163456941" style="zoom:50%;" />
<img src="https://ml2022.oss-cn-hangzhou.aliyuncs.com/img/image-20250518163442508.png" alt="image-20250518163442508" style="zoom:50%;" />
