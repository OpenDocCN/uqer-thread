# 哪位大神帮我解读以下下面的语句是什么意思

        sell_list = context.security_position
        for stk in sell_list:
            order_to(stk, 0)

        # 再买入
        buy_list = RSI.index
        total_money = context.reference_portfolio_value
        prices = context.reference_price 
        for stk in buy_list:
            if np.isnan(prices[stk]) or prices[stk] == 0:  # 停牌或是还没有上市等原因不能交易
                continue
            order(stk, int(total_money * RSI.loc[stk]['wts'] / prices[stk] /100)*100)
    else:
        return
-------------------------------------------------------------------------------------------
--------------------
思路是什么呀？