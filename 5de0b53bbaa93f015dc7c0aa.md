# 飞天讲课：【AIOQuant量化交易框架】第1讲-高频交易介绍

**1.什么是高频交易**
一提到高频交易，对于大部人来说，高频交易是比较神秘的。在大部分人的认知里，高频交易有超强的盈利能力，堪比印钞机；纯粹靠交易赚钱，有着神秘的数学模型和尖端科技，精准的预测市场走势，带着无可比拟的优势在市场上呼风唤雨；利用速度优势割散户的肉，因此大家认为这是作弊。

高频交易（英语：High Frequency Trading，HFT），是指从那些人们无法利用的、极为短暂的市场变化中寻求获利的自动化程序交易，比如某种证券买入价和卖出价差价的微小变化，或者某只股票在不同交易所之间的微小价差。这种交易的速度如此之快，以至于有些交易机构将自己的“服务器群组”安置到了离交易所的服务器很近的地方，以缩短交易指令通过光缆以光速传送的时间。一般是以电脑买卖盘程式进行非常高速的证券交易，从中赚取证券买卖价格的差价。

美国证券交易委员会（SEC）对高频交易的定义：

使用超高速的复杂计算机系统下单；
使用 co-location 和直连交易所的数据通道；
平均每次持仓时间极短；
大量发送和取消委托订单；

**2. 高频交易的价值**
据统计，在一个成熟的交易市场里，高频交易充当着举足轻重的作用，甚至超过75%的交易量都是由程序化高频交易完成，他们一般是承担着做市商的角色，或者类似做市商角色。

做市商是市场流动性提供者，同时也是散户的对手盘。假如市场行情持续走势是单边行情，一般散户的交易行为在遇到单边行情的时候，都会呈现出只买或只卖的行为，这个时候做市商就充当着散户的对手盘角色，否则市场上就没人接单了。

高频交易一般会持续高效的对市场行情做分析处理，他们可以提前洞悉市场趋势，然后提前做出市场行情预判。一般高频交易每笔成交的利润比较低，甚至略微亏损来赚取大量的成交带来的手续费返利。做市商还可以提供流动性服务来收取交易所的服务费。

**3. 高频交易策略**
3.1 事件套利

某些重复性事件会对一些特定的市场产生短期的、可预见的影响。高频交易系统可通过这些预测制定出一套短期持仓组合。

3.2 统计套利

这类交易策略是通过挖掘哪些市场发生了暂时性的、可预测的统计偏离，进而获利。这种策略可被应用于所有的流动市场，如股票、债券、期货、外汇交易中。

3.3 低延迟策略

一些纯粹的高频交易极度依赖于对市场数据的超低延迟访问。在这种策略中，交易系统依靠在不同市场间极小的信息获取的速度优势来谋利。

3.4 新闻交易

行业内动态可以从各种渠道被获取，如社交、媒体、新闻、微博等。自动交易系统通过识别公司、项目、政策等关键字，甚至是进行语义分析，以求在人类交易员之前对这些消息做出反应。

**4. 币圈高频交易**
现阶段币圈还没有真正意义上的高频交易，币圈程序化交易还主要集中在中低频交易，主要原因有以下几点：

各大交易所公开的交易API规范不统一；
交易所技术瓶颈导致撮合速度慢；
交易手续费高，提币、转账速度慢；
缺乏优秀的量化交易框架；
正是因为诸多原因导致现阶段币圈的程序化交易频率并不高，而且几乎每个API都有请求次数限制，一般限制在每秒2~5次左右不等。

另外，有些交易所提供了专门的API给做市商使用，请求频率稍微高一些，一般能够达到每秒50次左右，但和传统金融机构的高频做市商相比也是相差甚远。

**5. AIOQuant框架介绍**
AIOQuant 是一套使用 Python 语言开发的 异步事件驱动的量化交易/做市系统，它被设计为适应中高频策略的交易系统，底层封装了操作系统的aio*库实现异步事件循环，业务层封装了 RabbitMQ消息队列实现异步事件驱动，再加上Python语言的简单易用，它非常适用于数字货币的高频策略和做市策略开发。

AIOQuant 同时也被设计为一套完全解耦的量化交易系统，其主要模块包括行情系统模块、资产系统模块、交易系统模块、风控系统模块、存储系统模块， 各个模块都可以任意拆卸和组合使用，甚至采用不同的开发语言设计重构，模块之间通过RabbitMQ消息队列相互驱动，所以不同模块还可以部署在不同的进程，或不同服务器。

**6. AIOQuant能够做什么**
AIOQuant 提供了简单而强大的功能：

基于 Python Asyncio 原生异步事件循环，处理更简洁，效率更高；
跨平台（Windows、Mac、Linux），可任意私有化部署；
任意交易所的交易方式（现货、合约）统一，相同策略只需要区别不同配置，即可无缝切换任意交易所；
所有交易所的行情统一，并通过事件订阅的形式，回调触发策略执行不同指令；
支持任意多个策略协同运行；
支持任意多个策略分布式运行；
毫秒级延迟（10毫秒内，一般瓶颈在网络延迟）；
提供任务、监控、存储、事件发布等一系列高级功能；
定制化Docker容器，分布式配置、部署运行；
量化交易Web管理系统，通过管理工具，轻松实现对策略、风控、资产、服务器等进程或资源的动态管理；
… …
**7. AIOQuant系统架构**
基于 AIOQuant 底层SDK可以开发一整套分布式交易系统。
![图片注释](http://storage-uqer.datayes.com/5dd5f9f2baa93f011a4d728a/2486ec60-126e-11ea-b01b-0242ac140002)

**8. 结束语**
本文主要是介绍了高频交易，同时分析了币圈无法做高频交易的原因，并以此为契机介绍了我们的 AIOQuant 量化交易框架。

AIOQuant 开源项目：https://github.com/JiaoziMatrix/aioquant
AIOQuant 作者提供了数字货币历史行情数据服务：https://jiaozi-matrix.com
视频地址：https://www.bilibili.com/video/av77324586/
