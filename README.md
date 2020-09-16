# CUHK - FINA4380 - Algorithmic Trading Strategies
Codes and projects for the course FINA4380 Algorithmic Trading Strategies at CUHK

## 1. Final Project - Regime Switching Statistical Hedge Strategy
### Strategy Idea and Motivation
The idea of the strategy originates from the various regime switching asset allocation strategies proposed by the finance community. In these strategies, dynamic asset allocation is performed when a switch in regime is detected. For example, Kritzman et al. (2012) proposed a two-regime Hidden Markov Model (HMM) to forecast regimes in market turbulence and constructed a dynamical asset allocation strategy. However, the asset allocation rules (weights on different asset classes) presented are arbitrarily defined as stated in the paper. 

Other regime detection methods are also proposed. For example, Nystrup, P., Hansen, B. W., Madsen, H., & Lindström, E. (2016) proposed an approach for dynamic asset allocation based on change points detected in CBOE VIX or daily returns of S&P 500 index using a non-parametric approach.

Greatly motivated by the above ideas, I hope to create a Regime Switching Strategy to achieve the following:
-	During low volatility regime, long S&P 500 E-mini futures 
-	During high volatility regime, hedge long position in S&P 500 futures with VIX futures using the appropriate hedge ratio
-	The central idea is to see if quantitative/algorithmic rules designed using Statistical techniques can be applied to identify 1) the correct hedging timing and 2) the correct hedging ratio to prevent the large drawdowns and outperform a simple long-and-hold strategy
-	Most important, the hedge timing and hedge ratio should not be predefined and are determined by data

### Table of Content
This section outlines the structure of the file ‘Regime Switching Statistical Hedge Final.ipynb’.
1.	Read Data, Clean Data and Descriptive Plots
2.	Determining Hedge Ratio using Kalman Filter
3.	Determining Hedge Timing
    a.	Determining Hedge Timing using Daily Equity Turbulence Index and Hidden Markov Model
    b.	Determining Hedge Timing using change point analysis
    c.	Discussion and Comparison on HMM Strategy and Change Point Strategy
4.	Execution and NAV Calculation on Change Point Strategy
5.	Performance Measure for Change Point Strategy

