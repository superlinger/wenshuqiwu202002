# 如何像CTO一样开发软件
![Diego PH on Unsplash](0!vTryr8NFyB-JR8_o)
> Diego PH on Unsplash


美国劳工统计局预测，到2020年，软件开发工作的职位将比仅在美国就职的申请人多140万。

当您了解到2018年全球有2300万软件开发人员时，该统计数字甚至更令人惊讶

这些数字意味着两件事：
+ 良好的软件开发实践是必须的
+ 关于“良好的开发实践”是什么样的，有很多意见，而要浏览这些意见可能是具有挑战性的

但是，在技术领域中也有一些人经常以访谈，博客和书籍的形式（例如，Martin Fowler或Kent Beck）分享他们对软件开发的意见，这些意见在软件世界中常常被认为是有价值的。 。

其中之一是Tripwire的创始人兼长期首席技术官Gene Kim。 Kim的著作广为人知，他的职业生涯对软件开发实践的重要性产生了深远的影响。 许多人熟悉他所写或合着的书：《凤凰计划》，《 DevOps手册》，《加速》，并高兴地看到他最新发行的《独角兽计划》。

Phoenix项目的重点是软件开发的DevOps /基础架构方面，而Unicorn项目则跟随技术主管和架构师Maxine，她试图在其开发团队中应对不断变化的需求和外部依赖项带来的挑战。 在Phoenix中，“三种方式”和“四种工作类型”描述了重要的概念，许多人今天使用这些概念来帮助定义DevOps。

在本文中，我想分享一些吉恩·金（Gene Kim）最好的作品的主要收获。
# 凤凰计划的要点：

凤凰城项目以小说的形式写成，小说由人物和故事情节组成，遵循IT经理Bill的想法，试图应对更快发展的期望以及许多团队在沟通和依赖性方面的挑战。 它说明了“ DevOps”运动如何通过遵循运动的基本原理（称为“三种方式”）来改造团队。
![](0!kegXRWEGugESp9Qc)
# 三种方式：
## 流动原理

将IT视为价值流。 几乎就像工厂生产线一样，其中每个组件都为生产线增加了价值，并期望仅需完成一次
+ 工作仅朝一个方向流动-下游
+ 缺陷永远不会传递到下游
+ 我们应该一直在努力改善流程
## 反馈原则

用作衡量流增加的价值的方法
+ 上游反馈回路应该到位
+ 反馈循环应尽可能短，这意味着对添加/更改的快速反馈
## 持续学习与实验原理

描述软件组织的文化和环境
+ 通过练习寻求掌握东西
+ 从成功和失败中学习
+ 促进实验，而不要担心实验和失败的可能性。
![The Three Ways as an image](0!xvp2z4Bz3gu8iq7b)
> The Three Ways as an image

# 四种工作类型：
+ 业务项目-这些是您的涉众要求您为客户做的项目
+ 内部项目-例如，将从本地托管的数据中心迁移到云提供商
+ 运营变更-可能类似于部署新功能
+ 计划外的工作-计划中的故障，错误或突然更改，需要立即采取行动。
# 加速的要点：

作为《 DevOps状况报告》中一项研究的结果，该报告是由世界各地各种规模的公司进行的一项调查，Accelerate发布了四个关键指标，研究人员确定这四个指标是低，中，高绩效之间的唯一区别 团队：
![](0!-0RjaWfiHefCuux1)
# 四个关键指标：
+ 变更的前置时间-客户或利益相关者提出请求与该请求作为代码进入生产之间的时间间隔
+ 部署频率-将变更部署到生产的频率
+ 平均恢复时间-从发现的故障中恢复需要多长时间
+ 变更失败百分比-您的变更百分比导致系统中的错误，失败或错误

低，中和高绩效团队之间的度量标准划分如下，最左边的列是高绩效团队：
![The Four Key Metrics for High, Medium, and Low performing teams](0!10eWxRh2rkHlofwr)
> The Four Key Metrics for High, Medium, and Low performing teams


Phoenix Project和Accelerate帮助建立了一个基础，许多团队仍在努力适应这一基础，软件开发团队可以在此基础上衡量其生产率和效率，同时直接了解他们在这些领域如何改进。
# 独角兽计划的收获：

随着“独角兽项目”的发布，Gene Kim扩展了他对软件开发的想法，以确定他认为是当今影响工程和业务的最重要挑战。 他将这些挑战归结为“五个理想”。 与《凤凰计划》相似，这本书是一本小说，具有不同的故事和场景，以说明这些学习情况并确认DevOps运动作为快速安全地交付软件的重要性。
![](0!SIjmN_Wa5Ux9k6pI)
# 五个理想：
## 局部性和简单性

这与开发团队可以在一个或多个位置进行本地代码更改而不会影响或需要各种其他团队和其他位置的程度有关。 我们都知道，无需别人帮助我们或为我们提供某些东西就可以自己做事更容易。 需要多个团队的协作来部署或开发新功能，只会因沟通不畅而导致延误和潜在挑战。

为了使它起作用，我们需要简单性，这意味着产品或软件本身的各个部分与其他部分之间是松散耦合的，并且在其实现和基础结构中应尽可能简化。
## 专注，流动与欢乐

开发人员的日常工作和工作方式直接影响开发人员的效率。 当开发人员可以专注于编写代码而没有延迟或依赖时，它会产生一种流动感，从而带来欢乐。 从书中：

“我们的工作是否充满无聊并等待其他人代表我们完成工作？ 我们是否盲目地对整个小部分进行研究，只在部署过程中看到一切都炸毁，导致消防，惩罚和倦怠时才看到我们的工作成果？ 还是我们以小批量（理想情况下为单件生产）工作，从而获得对我们工作的快速而持续的反馈？ “
## 改善日常工作

这个理想与技术债务和架构直接相关。 世界上最大的公司（通常称为FANG：Facebook，Amazon，Netflix，Google等）之所以如此成功和稳定，是因为他们做出了有意识的决定来摆脱现有的技术债务。 微软以一种文化着称，这种文化说，如果开发人员在功能开发或提高开发人员生产率之间做出选择，那么选择应该始终是开发人员生产率。 这本书的一句名言：

“亚马逊可能会在六年内花费超过10亿美元重新配置其所有内部服务，以使彼此分离。”
## 心理安全

正如《 DevOps状态报告》和Google Aristotle Project所显示的那样，团队成员可以放心地从错误中吸取教训，并承担风险，这对于团队的效率至关重要。 这是因为团队需要能够谈论他们所遇到的问题并感到安全，因为需要预防才能解决问题，这需要诚实和无畏。
## 以客户为中心

顾名思义，这种理想使我们想起团队正在开发或从事的工作是否对客户真正重要（他们是否愿意为此付出代价？）还是仅仅是为我们自身职能带来价值的事情？ 筒仓？ 如果我们的日常行为无法为客户提供价值，那么也许我们应该在其他方面开展工作。

这本书中我最喜欢的一些报价：

“ Hoare原则：“有两种编写代码的方法：编写代码如此简单，显然其中没有错误；编写代码如此复杂，以至于其中没有明显的错误。”

“惩罚性失败和“攻击信使”只会使人们掩盖错误，最终，所有对创新的渴望都将被彻底扑灭。”

“创建软件应该是一种协作和对话式的努力—个人需要相互交流才能为客户创造新的知识和价值。”

“技术债务是您下次要进行更改时的感受。”

这三本书帮助设计了一个模板或示例，开发团队在进行项目并试图提高效率时可以遵循这些模板或示例，许多最大，最成功的软件公司已经在使用这些思想。 我期待这个家伙写的下一本书。

你怎么看？ 他错过了什么吗？
```
(本文翻译自Tylor Borgeson的文章《How to develop Software like a CTO》，参考：https://medium.com/swlh/software-development-practices-as-recommended-by-gene-kim-c6f6e952309f)
```