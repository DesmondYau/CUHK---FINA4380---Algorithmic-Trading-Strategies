l# CUHK - FINA4380 - Algorithmic Trading Strategies
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
    
2. __Hedge Timing will be determined using two methods, namely Hidden Markov Model (HMM) and Change Point Analysis__. Performance of both methods will be compared and discussed in section 3C of ‘Regime Switching Statistical Hedge Final.ipynb’. <br/>
    Hidden Markov Model 
    - Idea is motivated by Kritzman, M., Page, S., & Turkington, D. (2012)
    - Assume a two-regime Markov-Switching model, i.e. we are either in low volatility or high volatility regime
    - An equity turbulence index will be created using returns of 11 S&P 500 sector indices using multivariate distance measure/squared Mahalanobis distance which will be used to determine the regime at any given time point 
    - The two-regime Markov-Switching model will first be calibrated using in-sample turbulence index and the Baum Welch Algorithm
    - The model will then be used to infer the probabilities for high volatility regime and low volatility regime for the out-of-sample period using the Forward Algorithm.
    - Probability (high volatility regime) > Probability (low volatility regime) will indicate a classification of ‘high volatility regime’

