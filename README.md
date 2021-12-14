# FINRL-Test
This is a test of the FINRL libraries

...

This project is the result from the article "How To Automate The Stock Market Using FinRL (Deep Reinforcement Learning Library)?" written by Mohit Maithani (2021, May 1)

I'm using Python version 3.9.2.  First of all, this is a first attempt at using the FINRL libraries while pursuing a personal interest in backtesting trading strategies.  The

following changes were necessary to make it work:

1.  A fresh install of FINRL was required using PIP

2.  In the file (site-packages\finrl\env\env_stocktrading the lines from 209 to 213 needed to be commented out because of logging issues.

            # Add outputs to logger interface
            #logger.record("environment/portfolio_value", end_total_asset)
            #logger.record("environment/total_reward", tot_reward)
            #logger.record("environment/total_reward_pct", (tot_reward / (end_total_asset - tot_reward)) * 100)
            #logger.record("environment/total_cost", self.cost)
            #logger.record("environment/total_trades", self.trades)

3.  For this test I'm only using the TD3 learning model.  The learning rate and total_timesteps had to be changed.

            agent = DRLAgent(env = env_train)
            TD3_PARAMS = {"batch_size": 128, 
                          "buffer_size": 1000000, 
                          "learning_rate": 0.00003}

            model_td3 = agent.get_model("td3",model_kwargs = TD3_PARAMS)
            trained_td3 = agent.train_model(model=model_td3, 
                                         tb_log_name='td3',
                                         total_timesteps=60000)
							
              

4.  For Trading, I'm using e_trade_gym for the environment:

  df_account_value, df_actions = DRLAgent.DRL_prediction(model=trained_td3, environment = e_trade_gym) 


5.  The following code was added to save the results to a .pdf file



	    from matplotlib.backends.backend_pdf import PdfPages

	    def save_results(filename):
	       pp = PdfPages(filename)
	       fig_nums = plt.get_fignums()
	       figs = [plt.figure(n) for n in fig_nums]
	       for fig in figs:
		  fig.savefig(pp, format='pdf')
	       pp.close()


	    filename = "results.pdf"
	    save_results(filename)
    
6.  I'm also using the following code to save the account value to a file:

	    df_account_value.to_csv('account_value.csv')

7.  And the following to read the account value from a file:

		# backtest plot - from file

		import pandas as pd
		import matplotlib.pyplot as plt
		from matplotlib.backends.backend_pdf import PdfPages
		from finrl.trade.backtest import backtest_stats, backtest_plot, get_daily_return, get_baseline
		df_account_value = pd.read_csv (r'test_results.csv')
		backtest_plot(account_value=df_account_value, baseline_ticker = 'AAPL', baseline_start = '2019-01-01', baseline_end = '2021-01-01')


QUESTIONS FOR THE AUDIENCE...

1.  PyPlot is using 2 figures independently.  I'm looking for improvements on how to show figures as one file?  I'm not sure if my solution is sufficient.

2.  The TD3 algorithm is generating results that make the output remarkably close to the benchnark.  Is this correct?



Source:  

Maithani, M.  (2021, May 1).  How To Automate The Stock Market Using FinRL (Deep Reinforcement Learning Library)?
  https://analyticsindiamag.com/stock-market-prediction-using-finrl/

Liu, X.  Yang, H.  Chen, Q.  Zhang, Q.  Yang, L.  Xiao, B.  Wang, C.  (2020, Nov 19).  FinRL: A Deep Reinforcement Learning Library for Automated Stock Trading in Quantitative Finance.  https://arxiv.org/abs/2011.09607







