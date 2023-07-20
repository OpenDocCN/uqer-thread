# DataAPI.MktStockFactorsOneDayGet获取MA值问题

我用DataAPI.MktStockFactorsOneDayGet获取MA60值发现没有前复权
比如DataAPI.MktStockFactorsOneDayGet(tradeDate=u"20170323",secID=u"002607.XSHE",ticker=u"",field=u"secID,MA60",pandas="1")
获取002607.XSHE日期为20170323的MA60值为10.6248
但是该值明显为未经前复权的值，我在同花顺查到002607.XSHE日期为20170323的MA60前复权的值为5.87，而同花顺不做前复权的值为10.62