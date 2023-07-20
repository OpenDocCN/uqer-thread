# 股票行业分类DataAPI.EquIndustryGet的问题

获取方法是DataAPI.EquIndustryGet(industry=u"",secID=u"",ticker=u"000001,600001",industryVersionCD=u"010303",intoDate=u"",field=u"",pandas="1")
例如：
suoshuhangye = DataAPI.EquIndustryGet(secID=stock,industryVersionCD='010303',intoDate=date,pandas='1')['industryName3']
suoshuhangye = DataAPI.EquIndustryGet(secID=stock,industry='申万行业',intoDate=date,pandas='1')['industryName3']
上面那句不报错，但是下面那句是报错的。
请问industry=这个能用吗。
或者告诉我同花顺行业的industryVersionCD也可以。
我想用同花顺行业试试。