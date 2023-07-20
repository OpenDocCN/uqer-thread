# 为什么无法给优矿函数提取出来的数据赋值？？？

for stock in universe:
    stock_df = DataAPI.MktStockFactorsOneDayGet(secID=stock,
                                  tradeDate=u"20190509",
                                  field=u"secID,ticker,tradeDate,PB,PE",
                                  pandas="1")
以上代码提取出优矿基础数据，然后用stock_df[0,'PE'] = 20 为第一行的PE重新赋值，但是都是赋值不了，为什么呢？