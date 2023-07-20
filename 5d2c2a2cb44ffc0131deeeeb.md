# 在history中，能否拿到前复权的收盘价？

 history = context.history(current_universe, ['highPrice', 'lowPrice', 'closePrice', 'KDJ_J'], 60,style = 'sat', freq = '1d', rtype='array') 
 
 在回测历史数据的时候可以拿到收盘价，但是这个收盘价是除权的，该如何拿前复权的收盘价呢？