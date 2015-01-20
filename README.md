### Claim Paid Losses (Mercury Insurance Group)
###### _Jenny Liang (Wen Liang)_


----
#### Introduction

[Glenn Meyers and Peng Shi](http://www.casact.org/research/index.cfm?fa=loss_reserves_data) provide databases that include major personal and commercial lines of business from U.S. property casualty insurers. For the purpose of this project, only
the data involving Mercury Insurance Group will be used. Unfortunately, the datasets that contain information for Mercury Insurance Group are only the Workers’ Compensation and Other Liability datasets. I choose Other Liability line of business dataset because Mercury’s information from Workers’ Compensation mostly is zero. 

The Claim Paid Losses in Other Liability Insurance for accident years in 1988-1997 form a development triangle as below. Presenting the data in a triangle structure is because (1) It shows the development of claims over time for each origin period; (2) “ChainLadder” package in R expect triangles as input data sets with development periods along the columns and the origin period in rows

![](https://raw.githubusercontent.com/wliang88/ClaimsLossAnalysis/master/Triangle_Data.png)

#### Objective
Using the “ChainLadder” Package in R, I aim to forecast the future claims development for the bottom right half of the triangle. In other words, I am estimating unobserved values and quantify the outstanding loss liabilities for each origin year. 
#### Data
The cumulative claims development is visualized in the below Figure. Most of the insured incidents take 3-6 years to settle down and close. The growth rate is the highest after 2 - 3 years from the claims origin point but it does slow down with the increasing development period. 
![](https://raw.githubusercontent.com/wliang88/ClaimsLossAnalysis/master/Rplot_CumClaim.png)

The R package ChainLadder comes with two helper functions, cum2incr and incr2cum. They can transform cumulative triangles into incremental triangles and vice versa. Incremental claims for the development years are displayed in the figure below. For this data set, the highest incremental claims come from the third, fourth and fifth development years. In general, the decreasing trend in claims development is visible.
![](https://raw.githubusercontent.com/wliang88/ClaimsLossAnalysis/master/Rplot_IncData.png)
 
For a better illustration, the development of the incremental claims is shown in below figure individually for each origin period.

![](https://raw.githubusercontent.com/wliang88/ClaimsLossAnalysis/master/Rplot_IndivInc.png)

#### Chain-Ladder Methods

Chain-Ladder is a deterministic algorithm to forecast claims based on historical data. It assumes that the proportional developments of claims from one development period to the next are the same for all origin years.

##### Loss Development Factor method
Loss Development Factor method uses the basic chain ladder function, which link ratios are calculated as the volume weighted average development ratios of a cumulative loss development triangle from one development period to the next.

Since the oldest origin year is not fully developed, I extrapolate anther 100 development periods assuming a log-linear model. The link ratios then allow me to plot the expected claims development patterns. 

![](https://github.com/wliang88/ClaimsLossAnalysis/blob/master/Rplot_pattern.png)

The link ratios are then applied to the latest known cumulative claims amount to forecast the next development period. An ultimate column is appended to the right to accommodate the expected development beyond the oldest year (10) of the triangle due to the tail factor (1.005696) being greater than unity. 

![](https://raw.githubusercontent.com/wliang88/ClaimsLossAnalysis/master/FullData_LDFmethod.png)

Based on the estimated unobserved values, I can then draw the complete graph of Incremental claims for each origin year. The total estimated outstanding loss under this method is about 29259. 
![](https://raw.githubusercontent.com/wliang88/ClaimsLossAnalysis/master/Rplot_LDFmethod_FullDataset.png)

##### Mack-chain-ladder Method

The Mack-chain-ladder model can be regarded as a weighted linear regression through the origin for each development period: lm(y ~ x + 0, weights=w/x^(2-
alpha)), where y is the vector of claims at development period k + 1 and x is the
vector of claims at development period k.

The Mack method is implemented in the ChainLadder package via the function
MackChainLadder. I can then access the loss development factors and the full triangle. 

![](https://raw.githubusercontent.com/wliang88/ClaimsLossAnalysis/master/FullData_MCLmethod.png)

To check whether Mack chain-ladder method is valid for the dataset, we can see that there are no trends in all the residual plots. 

![](https://raw.githubusercontent.com/wliang88/ClaimsLossAnalysis/master/Rplot_MCLmethod.png)
 
I then plot the development, including the forecast and estimated standard errors by origin period by setting the argument lattice=TRUE.
![](https://raw.githubusercontent.com/wliang88/ClaimsLossAnalysis/master/Rplot_IndivMCLmethod.png)
 
### Further question
Forecast future claims development beyond development age 10? 

### Bibliography
Glenn Meyers and Peng Shi,            
	http://www.casact.org/research/index.cfm?fa=loss_reserves_data

Package ‘ChainLadder’
http://cran.r-project.org/web/packages/ChainLadder/ChainLadder.pdf


