# CUHK - FINA4380 - Algorithmic Trading Strategies
Codes and projects for the course FINA4380 Algorithmic Trading Strategies at CUHK

<br/>

## 1. Final Project - Regime Switching Statistical Hedge Strategy
### Strategy Idea and Motivation
The idea of the strategy originates from the various regime switching asset allocation strategies proposed by the finance community. In these strategies, dynamic asset allocation is performed when a switch in regime is detected. For example, Kritzman et al. (2012) proposed a two-regime Hidden Markov Model (HMM) to forecast regimes in market turbulence and constructed a dynamical asset allocation strategy. However, the asset allocation rules (weights on different asset classes) presented are arbitrarily defined as stated in the paper. 

Other regime detection methods are also proposed. For example, Nystrup, P., Hansen, B. W., Madsen, H., & Lindström, E. (2016) proposed an approach for dynamic asset allocation based on change points detected in CBOE VIX or daily returns of S&P 500 index using a non-parametric approach.

Greatly motivated by the above ideas, I hope to create a Regime Switching Strategy to achieve the following:
-	__During low volatility regime, long S&P 500 E-mini futures__
-	__During high volatility regime, hedge long position in S&P 500 futures with VIX futures using the appropriate hedge ratio__
-	The central idea is to see if quantitative/algorithmic rules designed using Statistical techniques can be applied to identify 1) the correct hedging timing and 2) the correct hedging ratio to prevent the large drawdowns and outperform a simple long-and-hold strategy
-	Most important, the hedge timing and hedge ratio should not be predefined and are determined by data

<br/>

### Table of Content
This section outlines the structure of the file ‘Regime Switching Statistical Hedge Final.ipynb’.
1.	Read Data, Clean Data and Descriptive Plots
2.	Determining Hedge Ratio using __Kalman Filter__
3.	Determining Hedge Timing
    - Determining Hedge Timing using Daily Equity Turbulence Index and __Hidden Markov Model__
    - Determining Hedge Timing using __change point analysis__
    - Discussion and Comparison on HMM Strategy and Change Point Strategy
4.	Execution and NAV Calculation on Change Point Strategy
5.	Performance Measure for Change Point Strategy

<br/>

### Strategy Construction and Methodology
This section outlines the specific methods used for determining the Hedge Ratio and Hedge Timing used in the trading strategy.
1. __Hedge Ratio will be determined using Kalman Filter Method.__
    - Linear State-Space model <br/> 
            <p align="center"> <a href="https://www.codecogs.com/eqnedit.php?latex=\begin{bmatrix}\alpha_{t&plus;1}\\\beta_{t&plus;1}\end{bmatrix}&space;=&space;\begin{bmatrix}1&space;&&space;0\\0&space;&&space;1\end{bmatrix}&space;\begin{bmatrix}\alpha_{t}\\\beta_{t}\end{bmatrix}&space;&plus;&space;\begin{bmatrix}\eta_{t}\\\epsilon_{t}\end{bmatrix}" target="_blank"><img src="https://latex.codecogs.com/svg.latex?\begin{bmatrix}\alpha_{t&plus;1}\\\beta_{t&plus;1}\end{bmatrix}&space;=&space;\begin{bmatrix}1&space;&&space;0\\0&space;&&space;1\end{bmatrix}&space;\begin{bmatrix}\alpha_{t}\\\beta_{t}\end{bmatrix}&space;&plus;&space;\begin{bmatrix}\eta_{t}\\\epsilon_{t}\end{bmatrix}" title="\begin{bmatrix}\alpha_{t+1}\\\beta_{t+1}\end{bmatrix} = \begin{bmatrix}1 & 0\\0 & 1\end{bmatrix} \begin{bmatrix}\alpha_{t}\\\beta_{t}\end{bmatrix} + \begin{bmatrix}\eta_{t}\\\epsilon_{t}\end{bmatrix}" /></a> </p>
            <p align="center"> <a href="https://www.codecogs.com/eqnedit.php?latex=SP\_return_{t}&space;=&space;\begin{bmatrix}1&space;&&space;VX\_return_{t}\end{bmatrix}&space;\begin{bmatrix}\alpha_{t}\\\beta_{t}\end{bmatrix}&space;&plus;&space;e_{t}" target="_blank"><img src="https://latex.codecogs.com/svg.latex?SP\_return_{t}&space;=&space;\begin{bmatrix}1&space;&&space;VX\_return_{t}\end{bmatrix}&space;\begin{bmatrix}\alpha_{t}\\\beta_{t}\end{bmatrix}&space;&plus;&space;e_{t}" title="SP\_return_{t} = \begin{bmatrix}1 & VX\_return_{t}\end{bmatrix} \begin{bmatrix}\alpha_{t}\\\beta_{t}\end{bmatrix} + e_{t}" /></a> </p> <br/>
    - The initial state covariance, transition covariance of <a href="https://www.codecogs.com/eqnedit.php?latex=\begin{bmatrix}\eta_{t}\\\epsilon_{t}\end{bmatrix}" target="_blank"><img src="https://latex.codecogs.com/svg.latex?\begin{bmatrix}\eta_{t}\\\epsilon_{t}\end{bmatrix}" title="\begin{bmatrix}\eta_{t}\\\epsilon_{t}\end{bmatrix}" /></a>  and observation covariance of  <a href="https://www.codecogs.com/eqnedit.php?latex=e_{t}" target="_blank"><img src="https://latex.codecogs.com/svg.latex?e_{t}" title="e_{t}" /></a>  will be estimated using Expectation Maximization (EM) 
<br/>
    
2. __Hedge Timing will be determined using two methods, namely Hidden Markov Model (HMM) and Change Point Analysis__. Performance of both methods will be compared and discussed in section 3C of ‘Regime Switching Statistical Hedge Final.ipynb’. <br/> <br/>
    
    Hidden Markov Model 
    - Idea is motivated by Kritzman, M., Page, S., & Turkington, D. (2012)
    - Assume a two-regime Markov-Switching model, i.e. we are either in low volatility or high volatility regime
    - An equity turbulence index will be created using returns of 11 S&P 500 sector indices using multivariate distance measure/squared Mahalanobis distance which will be used to determine the regime at any given time point 
    - The two-regime Markov-Switching model will first be calibrated using in-sample turbulence index and the Baum Welch Algorithm
    - The model will then be used to infer the probabilities for high volatility regime and low volatility regime for the out-of-sample period using the Forward Algorithm.
    - Probability (high volatility regime) > Probability (low volatility regime) will indicate a classification of ‘high volatility regime <br/> <br/>
    
    Change Point Analysis
    - Idea is motivated by Nystrup, P., Hansen, B. W., Madsen, H., & Lindström, E. (2016) and Gordon J. Ross (2015). Parametric and Nonparametric Sequential Change Detection in R
    - Given a sequence <a href="https://www.codecogs.com/eqnedit.php?latex=X_{1},X_{2},......,&space;X_{n}" target="_blank"><img src="https://latex.codecogs.com/svg.latex?X_{1},X_{2},......,&space;X_{n}" title="X_{1},X_{2},......, X_{n}" /></a>, the Change Point Analysis works by evaluating a two-sample test statistic at every possible split point. Let <a href="https://www.codecogs.com/eqnedit.php?latex=D_{k,n}" target="_blank"><img src="https://latex.codecogs.com/svg.latex?D_{k,n}" title="D_{k,n}" /></a> be the value of the test statistic when the sequence is split into the two samples { <a href="https://www.codecogs.com/eqnedit.php?latex=X_{1},&space;X_{2},&space;......,&space;X_{k}," target="_blank"><img src="https://latex.codecogs.com/svg.latex?X_{1},&space;X_{2},&space;......,&space;X_{k}," title="X_{1}, X_{2}, ......, X_{k}," /></a> } and {<a href="https://www.codecogs.com/eqnedit.php?latex=X_{k&plus;1},&space;X_{k&plus;2},&space;......,&space;X_{n}," target="_blank"><img src="https://latex.codecogs.com/svg.latex?X_{k&plus;1},&space;X_{k&plus;2},&space;......,&space;X_{n}," title="X_{k+1}, X_{k+2}, ......, X_{n}," /></a>}, and define <a href="https://www.codecogs.com/eqnedit.php?latex=D_{n}" target="_blank"><img src="https://latex.codecogs.com/svg.latex?D_{n}" title="D_{n}" /></a> be the the maximum of all <a href="https://www.codecogs.com/eqnedit.php?latex=D_{k,n}" target="_blank"><img src="https://latex.codecogs.com/svg.latex?D_{k,n}" title="D_{k,n}" /></a>. <a href="https://www.codecogs.com/eqnedit.php?latex=D_{n}" target="_blank"><img src="https://latex.codecogs.com/svg.latex?D_{n}" title="D_{n}" /></a> is then compared to some threshold, with a change being detected if the threshold is exceeded
    - In the sequential context (data coming in every day continuously), the observations are processed one-by-one, with <a href="https://www.codecogs.com/eqnedit.php?latex=D_{t}" target="_blank"><img src="https://latex.codecogs.com/svg.latex?D_{t}" title="D_{t}" /></a> being computed based on the first t observations. <a href="https://www.codecogs.com/eqnedit.php?latex=D_{t&plus;1}" target="_blank"><img src="https://latex.codecogs.com/svg.latex?D_{t&plus;1}" title="D_{t+1}" /></a> being computed based on the first t+1 observations and so on. The change detection time is defined as the first value of t where the threshold is exceeded. Supposing this occurs at time t = T, then the best estimate of the location of the change point is the value of k which maximized <a href="https://www.codecogs.com/eqnedit.php?latex=D_{k,T}" target="_blank"><img src="https://latex.codecogs.com/svg.latex?D_{k,T}" title="D_{k,T}" /></a>. Writing <a href="https://www.codecogs.com/eqnedit.php?latex=\hat{\tau}" target="_blank"><img src="https://latex.codecogs.com/svg.latex?\hat{\tau}" title="\hat{\tau}" /></a> for this, we have <a href="https://www.codecogs.com/eqnedit.php?latex=\hat{\tau}" target="_blank"><img src="https://latex.codecogs.com/svg.latex?\hat{\tau}" title="\hat{\tau}" /></a> <a href="https://www.codecogs.com/eqnedit.php?latex=\leq" target="_blank"><img src="https://latex.codecogs.com/svg.latex?\leq" title="\leq" /></a> T
    - Specifically, we assume return of S&P 500 futures follows normal distribution approximately (see in-sample distribution plot for S&P 500 futures returns). The Bartlett test statistic, which detects variance change in Gaussian sequence, will be used
    
<br/>

### Strategy Characteristics
   - Instruments: S&P 500 E-mini futures and S&P 500 VIX futures
   - Long-short, relative return strategy
     - Benchmark Portfolio: Long and hold S&P 500 E-mini futures
   - Out-of-sample trading period: Jan 2017 – June 2020 
     - Market in this period is volatile enough to test the strategy
   - Aims at 0% cash holding
   - VIX futures is used for hedging 
    
<br/>

### Rebalancing and Risk off trigger
   - Rebalancing is performed when either one of the two following conditions is satisfied: 
     - When a change in regime is detected (either from low volatility regime to high volatility regime or from high volatility regime to low volatility regim)
     - When the current hedge ratio deviates from the target hedge ratio by a certain threshold
   - Since hedging using VIX futures is only a statistical hedge (i.e. the hedge ratio is ‘correct’ on average), we only perform rebalancing when the current hedge ratio deviates from target hedge ratio by more than 5% to reduce transaction cost
   - There is no predefined risk off trigger for this strategy since the central idea of the strategy is to detect high volatility regime using the data and perform hedging at the correct timing with the correct hedge ratio to avoid large drawdown

<br/>

### Transaction cost assumption
   1. Transaction cost assumption for ES-mini:
      - Commission: USD 0.85 per contract
        -   https://www.interactivebrokers.com/en/index.php?f=1590&p=futures1
      -	Exchange fee: USD 1.23 per contract 
        -	https://www.tradestation.com/pricing/exchange-execution-and-clearing-fees/
      -	NFA Regulatory Fee: USD 0.02 per contract 
        -	https://www.tradestation.com/pricing/exchange-execution-and-clearing-fees/
      -	Bid-Ask Spread: USD 12.5 per contract (0.25 per tick * 50)
      -	Market Impact: Assume 0.125 bps 
        -	https://www.cmegroup.com/trading/equity-index/report-a-cost-comparison-of-futures-and-etfs.html
        -	According to research from CME, the market impact of the hypothetical USD 100 million order is estimated to be 1.25bps for E-mini futures. Since we have USD 10 million capital, assume 0.125 bps for simplicity <br/>

   2. Transaction cost assumption for VX:
      -	Commission: USD 0.85 per contract 
        -	https://www.interactivebrokers.com/en/index.php?f=1590&p=futures1
      -	Exchange fee: USD 1.49 per contract 
        -	https://www.tradestation.com/pricing/exchange-execution-and-clearing-fees/
      -	NFA Regulatory Fee: USD 0.02 per contract 
        -	https://www.tradestation.com/pricing/exchange-execution-and-clearing-fees/
      -	Bid-Ask Spread: USD 50 per contract (0.05 per tick * 1000) 
      -	Market Impact: Assume 0.125 bps for simplicity with the reason same as above

<br/>


### Reference
    - Kritzman, M., Page, S., & Turkington, D. (2012). Regime Shifts: Implications for Dynamic Strategies (corrected). Financial Analysts Journal, 68(3), 22-39. doi:10.2469/faj.v68.n3.3
    - Nystrup, P., Hansen, B. W., Madsen, H., & Lindström, E. (2016). Detecting change points in VIX and S&P 500: A new approach to dynamic asset allocation. Journal of Asset Management, 17(5), 361-374. doi:10.1057/jam.2016.12
    - Gordon J. Ross (2015). Parametric and Nonparametric Sequential Change Detection in R: The cpm Package. Journal of Statistical Software, 66(3), 1-20. URL http://www.jstatsoft.org/v66/i03/


