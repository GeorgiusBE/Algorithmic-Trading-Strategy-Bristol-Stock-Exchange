## 1	Trading Strategy: MMM01

## 2	Trading Strategy: MMM02
MMM02 utilizes 2 moving averages (short-term MA and long-term MA), where the MA is based on past transaction prices. For context, I have explored and tested var-ious trading signals that can be generated using 2 moving averages, most notably the MA crossover strategy, where it performed very poorly due to its restrictive buying rule, which generates few signals and limits its effectiveness.

The best performing MMM02 out of the different dual-MA strategies that have been tested is the one that acts like a market maker, generating profit from the bid-ask spread. This strategy is long-only, with a maximum inventory of one unit. A buy signal is triggered when the short-term MA (calculated with a window size of 5) falls below 90% of the long-term MA (calculated with a window size of 10). Upon generating a buy signal, MMM02 places a limit bid order to purchase one unit of the security at the best bid price plus 1. This ensures the quote takes priority in the Lim-it Order Book (LOB). Also note that, to allow the market to settle before submitting any quote, MMM02 can only make its first purchase after the initial 5 minutes have passes. In terms of selling rule, once the MMM02 owns one unit of the security, MMM02 places an ask order to sell its single unit at the best ask price minus 1, again ensuring the quote is prioritized in the LOB.

The rationale behind this strategy is that, by leveraging the long-term and short-term MA to determine undervaluation of the security, MMM02 only generates a buy sig-nal when there is a sustained undervaluation of the security. Thus, in theory, the price is more likely to go up rather than down.  This is in contrast to MMM01 where the slightest hints of undervaluation will end up in a purchase. In such case, the probability of the price going up and down may be approximately to equal.

Furthermore, MMM02 avoids immediate execution by setting its bid price at the best bid rather than crossing the spread to meet the best ask. Observations from the LOB indicate a substantial bid-ask spread, making this tactic particularly advanta-geous as it avoids the cost of the spread. Moreover, with the strategy’s relatively non-restrictive selling rule, MMM02 has quick turnover, enabling it to capitalize on the bid-ask spread very quickly and repeatedly, ultimately generating significant profits through high volumes of trades.

## 3	Market Setup
The goal is to create a market environment that mimics real-world trading environ-ment. In the simulation, there will be a diverse mix of 20 trading agents (i.e. sales traders), distributed as described below, to ensure a diverse mix of traders typically observed in financial markets,
- Giveaway (5%): These traders place bid/ask quotes order right at the customer-order limit price. This is akin to unsophisticated traders who prioritize trade ex-ecution.
- ZIC (15%): ZIC traders place quotes randomly, under the constraint that they are within the customer-order limit price (i.e. not loss making). They represent uninformed traders who contribute to the noise in real-world market.
- Shaver (5%): When a Shaver buyer and seller are viewed as a combined entity, their behavior closely mirrors that of scalpers, aiming to swiftly capture the bid-ask spread.  
- Sniper (5%): They represent funds who are performing last-minute portfolio rebalancing before the market closes, retail traders who are closing their posi-tions to avoid overnight risk, or traders who exploit last-minute price ineffi-ciencies.
- ZIP (30%): ZIP traders represent market making firms that use simplified rules based on recent transaction price to modify their quotes. Given the prevalence of using recent transaction data in the market, ZIP traders have the highest pro-portion in the market.
- ZIPSH (15%): An extension of ZIP that uses Stochastic Hill Climbing optimiza-tion process to iteratively optimize ZIP’s hyper-parameters. Thus, it resembles a more sophisticated and experimental trading strategy, such as algorithmic trad-ers who are continuously adjusting/optimizing their trading strategies for in-cremental improvements.
- PRZI (15%): Depending on the parameter value ‘s’, PRZI traders act like Give-away, ZIC, Shaver, or a hybrid between these 3. Thus, improving the diversity of traders in the market.
- PRSH (10%): A variation of PRZI that uses the Stochastic Hill Climbing optimi-zation process to iteratively make small adjustments (mutations) to the current strategy (i.e. the ‘s’ parameter).

So, each market simulation includes these 20 agents and one MMM trader. The MMM trader always starts with an initial balance of $500.
The supply and demand price ranges are set between 50–100 and 150–200, respec-tively. The reason is to drive market activity as in liquid markets, since buyers have a relatively large customer-order limit price and sellers have a relatively small cus-tomer-order limit price. Moreover, both curves use ‘jittered’ step-mode because it introduces randomness to otherwise structured price levels, similar to the real-world where the supply and demand curves are not perfectly uniform or completely ran-dom.
Moreover, the duration for each market session is 7.5 hours long and the time inter-val between new customer-orders is 20 seconds. Lastly, the time-mode for the dis-tribution of customer-orders is ‘drip_poisson’ because, typically in the real world, customer-orders do not arrive at regular intervals, instead it is influenced by ran-dom processes, such as news events or random individual decisions.

## 4	Experiment
The experiment consists of 3 different categories: (i) no market shock, (ii) positive market shock, and (iii) negative market shock. In each of the experiment, the per-formance of MMM01 and MMM02 are presented with total profit as the chosen performance metric.

### 4.1	No Market Shock
In this section, no market shocks are applied throughout the simulation. However, offset values derived from real-world price data are incorporated. The section is divided into two experiments based on the source of the price data used to calculate the offsets: (a) IBM and (b) Nasdaq. These datasets were selected for their compa-rability, as they originate from the stock market, which behaves differently from commodity and bond markets. This comparability enables a more reliable assess-ment of whether a strategy's outperformance is due to genuine superiority or merely a fluke.

#### (a) IBM

  
Fig. 1. The cumulative profit for trader MMM01 and MMM02 from a sample simu-lation with IBM offset values and no market shock.

As shown in Fig. 1., when offset values derived from IBM price is used, MMM01 far outperforms MMM02. In the specific sample run depicted, MMM01 accumulated over $10,396 in total profit, compared to approximately $2,839 for MMM02. More-over, notice that MMM01 only has profitable trades due to MMM01’s selling rule. In contrast, MMM02’s cumulative profit sporadically moves up-and-down. This volatility arises from the nature of the BSE market, where a trader is randomly se-lected at each time step to place a quote, if any. Consequently, after MMM02’s bid quote is executed, it is unlikely to immediately place an ask quote in the next time-step. During this delay, the LOB may move unfavorably relative to MMM02’s long position, introducing inventory risk and forcing MMM02 to place ask quotes at a price below the previously executed bid quote.

The simulation was repeated multiple times, yielding an average total profit of $11,414 for MMM01, compared to $2,505 for MMM02. Again, it shows MMM01’s outperformance. However, to truly confirm this outperformance, statistical tests are conducted.

To determine the suitable statistical test, the normality of each group and the homo-geneity of variances between the two groups were first evaluated. The Shapiro-Wilk test was applied to sample data from both MMM01 and MMM02 to assess whether the underlying population distributions were normal. Since the samples were col-lected independently, the Shapiro-Wilk test is a suitable test. The test produces p-values of 0.54 for MMM01 and 0.65 for MMM02. Thus, at a significance level (α) of 0.05, the null hypothesis of normality cannot be rejected, indicating that the pop-ulation from which the sample total profit was drawn follows a normal distribution.

Next, Fisher’s F-test was used to evaluate the homogeneity of variances between the two groups. When the normality assumption holds, the Fisher's F-test is more pow-erful (i.e. higher probability of correctly rejecting the null hypothesis when the null is false) compared to other similar tests (such as Levene's test). The test produced an F-statistic of 8.24, with critical values of 0.10 (lower) and 9.60 (upper) at α = 0.05. Since the F-statistic falls within the critical range, the null hypothesis of equal variances cannot be rejected. This confirms that there is no significant difference in variances between the two groups.

Given the data’s normality, homogeneity of variances, and independence of sam-ples, the Student’s t-test was selected to test whether MMM01’s average profit sig-nificantly exceeds MMM02’s.

`H_0: mean_profit(MMM01) <= mean_profit(MMM02)`

`H_1: mean_profit(MMM01) > mean_profit(MMM02)`

The p-value obtained from the t-test is approximately zero. This means that at α = 0.05, the null hypothesis is rejected. In other words, the average profit of MMM01 is significantly higher than that of MMM02.

#### (b) Nasdaq
  
Fig. 2. The cumulative profit for trader MMM01 and MMM02 from a sample simulation with Nasdaq offset values and no market shock.

Comparing Fig. 2. and Fig. 1., a similar behavior is observed when offset values derived from Nasdaq price data are used. Following the same workflow as before, the average total profit was calculated. MMM01 once again demonstrated superior performance, achieving an average total profit of $11,319 compared to MMM02’s $2,501.

Next, statistical tests are used to test whether MMM01 significantly outperforms MMM02. Firstly, the Shapiro-Wilk test concluded that the total profit data for MMM01 and MMM02 are normally distributed with p-value of 0.37 and 0.42, re-spectively, at α = 0.05. Then, the F-test was performed, where it returned an f-statistic of 16.98, and upper and lower critical values (with α = 0.05) of 0.10 and 9.60. Since the f-statistic falls outside of the critical value range, it is concluded that the variance between the two groups are not homogeneous.

Given that the total profit data are normally distributed, variances are not homoge-neous, and the two groups are independent, Welch’s t-test was employed to deter-mine whether MMM01’s average total profit is significantly greater than MMM02’s. (Note that the hypotheses for the Welch’s t-test is the same as in section 3.1 (a)). With p-value of approximately zero (i.e. less than α = 0.05), the null is rejected, meaning that MMM01 significantly outperforms MMM02 in terms of average total profit.

### 4.2	Positive Market Shock
In this experiment, a positive market shock was introduced midway through the simulation (at time = 13500 seconds), shifting the demand and supply curves up-ward by 50 units. As a result, the new ranges for the supply and demand curves were 100–150 and 200–250, respectively. Also note that for this experiment, only the IBM offset values will be used.

From running the simulations, a similar outcome is obtained like from when there is no market shock. The average total profit for MMM01 and MMM02 are $10419 and $2635, respectively.

Just like before, the Shapiro-Wilk test was performed, which showed that both groups’ total profits are normally distributed, with p-values of 0.73 and 0.28 for MMM01 and MMM02, respectively; and the F-test concludes that there is a statisti-cally significant difference in the variances between the two groups (with f-statistic = 40.12, lower critical value = 0.10, and upper critical value = 9.6). Therefore, the Welch’s t-test was deployed, using the same hypotheses as in section 3.1(a). The test concluded that MMM01’s average total profit was significantly greater than MMM02’s; with p-value (0.01) < α (0.05).

### 4.3	Negative Market Shock
Similar to the positive market shock experiment, only IBM offset values will be used. However, in this experiment, the demand and supply curves are shifted downwards mid-way through the simulation (at time = 13500 seconds), causing the supply and demand curve ranges to drop to 10-50 and 100-150, respectively.

The plot for MMM01 in Fig. 3. reveals a distinct characteristic not observed in any of the previous plots, while the MMM02 plot exhibits similar patterns to earlier experiments. Notably, MMM01’s total profit dropped significantly to $3471. This is caused by MMM01’s selling strategy that never allows it to make a losing trade and the maximum inventory it stores is one. Consequently, if MMM01 purchased a se-curity before the negative market shock and did not sell it in time, it would be una-ble to exit the losing position after the market shock has happened. This explains the inactivity of MMM01 post the market shock (as represented by the horizontal lines). In contrast, MMM02 was less impacted by the negative market shock due to its selling strategy, which ensures it continues quoting at the best ask price, even if it means realizing a substantial loss. By doing so, MMM02 quickly clears its inven-tory, enabling it to recover losses through future profitable trades.
 
  
Fig. 3. The cumulative profit for trader MMM01 and MMM02 from a sample simulation with IBM offset values and negative market shock.

Despite the challenges posed by the negative market shock, the results from multi-ple simulations indicate that MMM01’s average profit ($4,296) remains higher than MMM02’s ($2,863). To verify whether this performance difference is statistically significant, Shapiro-Wilk test is performed first, which showed that the total profit data for MMM01 (p-value = 0.31) and MMM02 (p-value = 0.50) are normally dis-tributed; and the F-test returned an f-statistic value of 8.56, lower critical value of 0.10, and upper critical value of 9.60, which demonstrates that there is no signifi-cant difference between the variances. Given these results, the Student’s t test is used with the same hypotheses as in section 3.1(a). The test returned p-value (0.039) < α (0.05) which means that MMM01’s average total profit is significantly greater than MMM02.

## 5	Conclusion
In the absence of a market shock, we compared the performance of MMM01 and MMM02 using two different sets of offset values (from IBM and Nasdaq). The re-sults consistently showed that MMM01 outperformed MMM02 in both scenarios. Additionally, when a positive market shock was introduced, the performance of both models remained largely unchanged, with MMM01 continuing to outperform. In contrast, when a negative market shock was applied, MMM01's performance sig-nificantly declined due to its inability to sell losing positions, as dictated by its sell-ing rule. On the other hand, MMM02 was less affected by the negative shock. De-spite this, MMM01 still demonstrated superior overall performance compared to MMM02. All in all, MMM01 consistently outperformed MMM02 across various scenarios. Given MMM02’s quick turnover, if transaction costs are introduced, the performance discrepancy will stand-out even more.
