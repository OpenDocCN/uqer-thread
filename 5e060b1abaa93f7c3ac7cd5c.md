# 如何获取成交价数据？

p=context.current_price('159919.XSHE')
approximationAmount = int(account.cash / p)
order(fund, approximationAmount)
我想获取当时的成交价，通过成交价计算下单的股数，报错为'StockAccount' object has no attribute 'current_price'这是为什么呢？