# 说说海龟交易法则的基本原理，如何实现海龟交易策略？

原文地址：https://www.fmz.cn/digest-topic/8978
什么是海龟策略？
几乎所有的宽客(Quant)都听说过海龟交易策略，该策略以海龟交易法则为核心。海龟交易法则，起源于八十年代的美国，是一套简单有效的交易法则。这个法则以及使用这个法则的人的故事被写成了一本书——《海龟交易法则》，这是一本入门量化投资的经典书籍。

股票证券的程序化、量化交易以前门槛可不低，以前软件支持少，账户开户门槛极高。FMZ.CN（国内站）支持富途证券、中泰XTP，开通了富途证券就可以很方便的做程序化模拟盘、实盘测试。本篇我们就一起学习设计一个股票版本的多品种海龟交易策略，初期我们主要基于回测系统进行设计、研究，慢慢的扩展至富途证券的模拟盘（模拟账户）。

策略设计：
策略架构我们参考http://FMZ.CN上开源的「商品期货多品种海龟策略」。和商品期货版本一样，我们设计一个海龟交易逻辑管理对象的构造函数TTManager。构造的对象（obj）用来操作、管理每个股票的海龟交易逻辑的执行。


```
代码内容
var TTManager = {
    New: function(needRestore, symbol, initBalance, keepBalance, riskRatio, atrLen, enterPeriodA, leavePeriodA, enterPeriodB, leavePeriodB, useFilter,
        multiplierN, multiplierS, maxLots) {

        // subscribe
        var symbolDetail = _C(exchange.SetContractType, symbol)
        if (symbolDetail.VolumeMultiple == 0) {
            Log(symbolDetail)
            throw "股票合约信息异常"
        } else {
            Log("合约", symbolDetail.InstrumentName, "一手", symbolDetail.VolumeMultiple, "股, 最大下单量", symbolDetail.MaxLimitOrderVolume, ", 最小下单量", symbolDetail.VolumeMultiple)
        }
        
        var obj = {
            symbol: symbol,
            tradeSymbol: symbolDetail.InstrumentID,
            initBalance: initBalance,
            keepBalance: keepBalance,
            riskRatio: riskRatio,
            atrLen: atrLen,
            enterPeriodA: enterPeriodA,
            leavePeriodA: leavePeriodA,
            enterPeriodB: enterPeriodB,
            leavePeriodB: leavePeriodB,
            useFilter: useFilter,
            multiplierN: multiplierN,
            multiplierS: multiplierS
        }
        obj.maxLots = maxLots
        obj.lastPrice = 0
        obj.symbolDetail = symbolDetail
        obj.status = {
            symbol: symbol,
            recordsLen: 0,
            vm: [],
            open: 0,
            cover: 0,
            st: 0,
            marketPosition: 0,
            lastPrice: 0,
            holdPrice: 0,
            holdAmount: 0,
            holdProfit: 0,
            switchCount: 0,
            N: 0,
            upLine: 0,
            downLine: 0,
            lastErr: "",
            lastErrTime: "",
            stopPrice: '',
            leavePrice: '',
            isTrading: false
        }
        ...
```
股票市场和商品期货市场又有些差别，下面我们来一起分析一下这些差别，然后对于策略进行具体的修改、设计。

交易时间差别
我们需要单独设计一个函数，识别开盘休盘时间，如下函数设计，给构造函数TTManager返回的对象obj增加方法：

```
代码内容
obj.newDate = function() {
          var timezone = 8                                
          var offset_GMT = new Date().getTimezoneOffset() 
          var nowDate = new Date().getTime()              
          var targetDate = new Date(nowDate + offset_GMT * 60 * 1000 + timezone * 60 * 60 * 1000)
          return targetDate
      }

      obj.isSymbolTrading = function() {
          // 使用 newDate() 代替 new Date() 因为服务器时区问题
          var now = obj.newDate()
          var day = now.getDay()
          var hour = now.getHours()
          var minute = now.getMinutes()
          StatusMsg = "非交易时段"
          if (day === 0 || day === 6) {
              return false
          }
          if((hour == 9 && minute >= 30) || (hour == 11 && minute < 30) || (hour > 9 && hour < 11)) {
              // 9:30-11：30
              StatusMsg = "交易时段"
              return true 
          } else if (hour >= 13 && hour < 15) {
              // 13:00-15:00
              StatusMsg = "交易时段"
              return true 
          }            
          return false 
      }
```
交易方向的差别
商品期货有开仓、平仓。股票只有买、卖，没有开仓平仓。股票类似于现货，但是也有持仓，买入的股票会在GetPosition函数获取的持仓列表中显示。

需要我们对交易下单的部分做设计，增加函数：


```
代码内容
obj.sell = function(e, contractType, lots, insDetail) {
    ...
}

obj.buy = function(e, contractType, opAmount, insDetail) {
    ...
}
```
下单头寸计算
商品期货交易下单时是按照合约张数下单，一张合约根据其合约乘数代表一定量的商品（例如rb合约，一张代表10吨螺纹钢）。股票虽说也是有按手计算的（根据板块有的1手100股，有的500股，还有的200股）。但是下单的时候必须是股数，并且要能被一手的股数整除。不能整除的会报错。

这样就需要对海龟交易法计算头寸的部分做一定修改：



```
代码内容
          var atrs = TA.ATR(records, atrLen)
          var N = _N(atrs[atrs.length - 1], 4)

          var account = _C(exchange.GetAccount)
          var unit = parseInt((obj.initBalance-obj.keepBalance) * (obj.riskRatio / 100) / N / obj.symbolDetail.VolumeMultiple)
          var canOpen = parseInt((account.Balance-obj.keepBalance) / (lastPrice * 1.2) / obj.symbolDetail.VolumeMultiple)
          unit = Math.min(unit, canOpen)
          unit = unit * obj.symbolDetail.VolumeMultiple
          if (unit < obj.symbolDetail.VolumeMultiple) {
              obj.setLastError("可开 " + unit + " 手 无法开仓, " + (canOpen >= obj.symbolDetail.VolumeMultiple ? "风控触发" : "资金限制") + "。 可用: " + account.Balance)
              return
          }

          // 交易函数
          if (opCode == 2) {
              throw "股票不支持做空"
          }
```
策略注释
为了方便理解策略代码，我们对策略通篇注释。

由于篇幅限制请移步：https://www.fmz.cn/digest-topic/8978查看源码。

回测测试、研究
我们选择几只股票回测：600519.SH,600690.SH,600006.SH,601328.SH,600887.SH,600121.SH,601633.SH。

其它参数设置：

![图片注释](http://storage-uqer.datayes.com/622ee8e0e0d1bb01211deb24/a425e77a-a365-11ec-ba39-0242ac140002)
回测时状态栏信息输出：
![图片注释](http://storage-uqer.datayes.com/622ee8e0e0d1bb01211deb24/aab4104e-a365-11ec-ba39-0242ac140002)
![图片注释](http://storage-uqer.datayes.com/622ee8e0e0d1bb01211deb24/ae5b4e24-a365-11ec-965d-0242ac140002)
![图片注释](http://storage-uqer.datayes.com/622ee8e0e0d1bb01211deb24/b1d47544-a365-11ec-965d-0242ac140002)

可以观察到，海龟交易法这种趋势跟踪策略需要在有较大的行情时才会有较好的盈利。在行情反复震荡时可能会有一定回撤。
涨幅较大的贵州茅台贡献了整体收益的绝大部分，看来选股也是十分重要的因素。并且根据状态栏中显示的统计数据来看，海龟交易法的止损次数要远高于策略成功盈利次数。这也是策略的思路核心，用较小的头寸试错。一旦抓住趋势突破加仓，抓住肥尾。创造震荡期损失数倍的盈利。
完整策略：https://www.fmz.cn/strategy/346551
该策略仅用于回测研究，实盘请自行优化、修改。