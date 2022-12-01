# Credit Default Swap Index Basis Adjustment


The model serves the purpose of computing basis adjustments for credit spread curves of the constituent obligors of the indexes such that the market price of the index can be repriced exactly. These adjusted index constituent curves are then used to compute index base correlations and mapped base correlations for bespoke trades, price the standard index CDO tranches, and calculate risks for constituent obligors.  

In the submitted model, the basis adjustment is calculated by applying one stepwise constant term structure of scaling factors to all individual hazard rate curves. The market standard models are employed in the valuation of MTM of the quoted index via the constituent curves and quoted index premiums. The term structure of the scaling factor is computed through bootstrapping such that the MTMs of the indexes of all terms can be repriced.

The implementation of the submitted model was first verified by testing against an independently developed test model. The implications of underlying assumptions in the model were tested against several benchmark models. The internal error tolerance control in the bootstrapping and the choice of the flat term nodes of the indexes were assessed and found appropriate as well.

It is important to note that, from the viewpoint of mathematical modeling, there are many ways to make such adjustments and the methodology of adjustment is only one of the choices. Each choice bears different assumptions on the embedded index basis risk and, to our best knowledge to date, there is no generally accepted market convention of making such adjustment. The test results indicate that, as far as the calibration of the base correlation and the valuation of an index CDO trade are concerned, the differences among those choices are immaterial. It is also indicated that the differences mainly resides in the term nodes that are far from the index maturities, which may be material depending on the hedging strategy for index CDO trades.

The purpose of the submitted model is to make adjustment to the credit spread curve of each reference name in the credit default swap (CDS) index portfolio such that the market price of the index can be repriced using these individual curves [1]. The adjusted index constituent curves are then used to calculate index base correlations and mapped base correlations for bespoke CDO trades, price the standard CDO tranches, and calculate risks for constituent obligors.  

Market quoted CDS indices, such as CDX or iTraxx, are defined as the fair premium paid over a stream of risky coupons in exchange of default protection on standardized portfolios. Unlike a stock index it is not simply the weighted average of credit spreads for the reference names in the portfolio. An index has a fixed coupon (see https://finpricing.com/lib/FiBondCoupon.html) and is traded by price. It the market convention that this price can be converted to quoted spread   with a flat curve and 40% recovery rate assumptions. As a result, an index position at a quoted spread level   is equivalent to a portfolio of CDS on the reference names, where all CDSs are entered at the same spread . Individually, each CDS does not trade at spread , but the portfolio as a whole does. Furthermore, market quoted index assumes a flat credit spread curve and a recovery rate 0.4.
 
On the other hand, single name credit default swaps for the index constituent obligors have become very liquid. Although trading an index and a portfolio single name CDS of its constituent obligor are essentially equivalent, a basis can be observed between the market quoted spread and the index spread calculated using constituent curves.  Therefore an adjustment is needed in order to use the constituent credit spread curves to price the standard index CDO tranches for the purpose of computing marking-to-market (MTM), risks, base correlation curves, and loss ratios.

In the submitted model, the following procedures are employed to calculate the basis adjustments:

•	For each index starting from the one with shortest maturity (3y), MTM of the index is calculated by building one index hazard rate curve via the standard method for single name CDS [3].  
•	The MTM of the same index is calculated the market standard valuation method of pricing an index with constituent curves is employed [4, 5]. 
•	A constant adjusting factor is calculated and applied to all single name CDS hazard rate curves such that the MTMs, calculated using the two methods respectively, agree with certain error tolerance.
•	Bootstrapping over all terms to the longest maturity of the trade such that a stepwise linear term structure of adjusting factor is built. The adjusted hazard rate curves for the constituents can then directly used for the valuation of tranched indexes and computation of base correlations.

It is important to note that the model is an ad hoc adjustment. From the viewpoint of mathematical modelling, it is a free choice to make such adjustment once the computed adjusted curves can reprice the index market quotes. Several valid alternatives of making such adjustment have been discussed. If the credit curves are reasonably smooth and flat (which is indeed the case for index curves), the difference between the model and the alternative models are not very significant in the computation of base correlation curves. 

Different choices of adjustment model bear different assumptions of the index basis risk.  In order to manage the embedded basis risk in a structured product, it is important that adequate reserve is in place to absorb the embedded uncertainty. 

For different indexes different scaling factor curves are computed. There is a scenario that one obligor belongs to two different indexes. Therefore the adjusted curves should be used only for the valuation of a structured trade with exactly the same collateral pool as the one in which the curve adjustment is made.
 
Compared with the initial approved model, the new model is improved in the following three ways:

1.	The new model is built upon new Oscar/Fritz credit library while the old one is based on old credlib. 
2.	In the new model, the notional amount of each obligor is explicitly modelled to reflect the fact that it may change over time due to various reasons.
3.	In the new model a stepwise linear term structure of the adjusting factor is built and applied to the hazard rate curve. However, such adjustment is applied to the credit spread curve in the old model. Note that, from the viewpoint of mathematically modelling, both methods are valid. The new method is more computationally efficient. 

A CDS index is defined as a set of M CDS trades, (M=126 for CDX7 and M=124 for iTraxx6), in which each reference name is described by a credit spread curve  , a recovery rate  , and a notional amount  .  For each credit spread curve, there is a standard term structure defined as  ={1w, 1m, 3m, 6m, 1y, 2y, 3y, 4y, 5y, 7y, 10y, 20y}). 

Within the current credit derivatives framework a reduced form of default probability of a reference entity has been implemented and well maintained. Let the default time of ith CDS reference entity in the index portfolio be , a deterministic risk-neutral hazard rate, , is defined such that the default probability between s and s+ds  . With this definition the default probability functions built upon the hazard rate are
(1)	 = Survival probability,
(2)	 = Default probability,
(3)	  = Default probability density function.

Assume a series of indexes have maturities   (normally n=1, 2, 3, 4 terms, known as 3y, 5y, 7y, and 10y). The market quoted b/s spread and coupon levels of them are denoted as  and , respectively. The entire notional of the index is . For each index fee payments are at interval   (maturity of the index).  

A buyer of CDS index protection is buying credit protection on reference names in the underlying portfolio. When one default event occurs, the CDS index protection buyer physically settles to receive the protection payment   for the defaulted reference name.  After the settlement, the relevant hypothetical underlying CDS is eliminated from the portfolio, which then has a notional amount . 

There are two valuation legs for an index: fee and protection. The risk annuity associated with the fee leg can be calculated by

(4)  

Payment for the fee during the period   is made at   if default has not occurred;   is the day count fraction; D(s) is the discount factor.   is the weight of notional in the index. The weight for each obligor in CDX7 and iTraxx6 can be found in Appendix xx.

The value of the protection leg can be expressed as 

(5)	 ,
where   is the recovery rate for each obligor. The recovery rates for the constituent obligors in CDX7 and iTraxx6 can be found in Appendix I.

The b/e spread of the index can be determined by 

(6)	 .

And the MTM can be expressed as

(7)        

Note that, both Eqs. (4) and (5) are calculated by assuming each constituent obligor be an CDS contract with the maturity as that of the index. The valuation of the two legs in the single name CDS follows a generally accepted market standard methodology. The computation of index b/e spread the MTM by employing constituent curves of the index portfolio has been approved previously and consistent with the one proposed by Bear Sterns and Morgan Stanley.

In the CDS index market, the b/e spread is quoted as . One would assume that the quoted b/e spread is consistent with the b/e spread calculated using the constituent curves. However, although both the index quotes and the individual CDS for each constituent obligor are very liquid, there exists a basis between them, that is,

(8)	 

The differences come from two factors [4]. First, as discussed above this market quoted index and the one calculated via Eq. (6) bears different assumptions such as term structures and recovery rates. Second, there exists an index basis risk.

In the computation of base correlation curves, the constituent curves have to be used. Therefore a basis adjustment has to be made such that the constituent curves can exactly reprice the index quote.

Such adjustment can be achieved by applying a term structure of adjustment factor, which is defined as   with . Defining a subset of  for each , we have . The hazard rate curve for each constituent obligor is adjusted. For all term nodes , we have

(9)	 .

For any obligor, the survival probability is adjusted correspondingly with the adjusted hazard rate curves, which can be written as:

(10)	 

Appling this equation to Eqs. (4) and (5), and we can calculate the new b/e spread and MTM of the index. 

In the model, a bootstrapping algorithm is employed to calculate the term structure of the adjustment factor such that all index MTMs can be repriced. As indicated in Eq. (7),  only market quoted b/e spread and the underlying coupon is not enough the calculated the MTM of the index. Therefore, it is the market convention that the quoted b/e spread of an index is embedded with several standard assumptions. A flat spread and a 0.4 recovery rate for all obligors are assumed such that we can calculate MTM using the valuation model of a single name CDS. 

In the bootstrapping, a root finding error tolerance is set to be . In the computation of the index MTM, the total notional amount is set to be one. Therefore for a standard index portfolio in which a one billion notional amount is assumed, the model has a precision for less than 100 (USD for CDX and EUR for iTraxx). In the next section, pertinent test has been designed to assess this choice of the parameter. 

In the first version of model on the basis adjustment, it is actually the credit spread curves that are adjusted [2]. An iteration method is adopted to adjust the credit spread. For all  and 

(11)  

In each of iteration, the new hazard rate curves are built to calculate the index MTM via Eq. (7). However, in the new method the process of rebuilding the hazard rate curve is skipped hence more efficient.  For the purpose of benchmark, this model was implemented in the test model.

As indicated in Eqs.(7) and (11),  the adjustments are applied to either the hazard rate curve or the credit spread curve by multiplying a scaling factor. There is another way to directly add or minus an adjusting curve to the hazard rate curves. Mathematically, it can be expressed as:

(12)  

In this benchmark model, the basis risk is quantified as one constant spread for all obligors. This is only reasonable if the liquidity and credit spread levels of all obligors are similar. As for on the run CDX and iTraxx, this might be a good approximation while it is definitely not true for off the run indexes in which there exist some non-liquid and/or high spread obligors. 

As shown in section 2, the adjusted curves can still exactly reprice the index quotes and the major difference between the absolute adjustment and relative adjustment resides in the CDS term nodes which are far from the quoted index maturities. This indicates that the uncertainty incurred by the different adjusting methods can be greatly suppressed, if we do not intend to hedge an index with terms very different from the index maturities.

It is the market convention that the indexes are quoted as a flat single spread for each maturity date. For each index, we can either assume one term node, which is the index maturity, or assume a flat curve with a standard flat maturity (1m, 3m, etc). In the model, the two nodes credit spread curve is assumed, which include a one-month term and maturity. A series of test are designed to test the choice, by assuming a single node, two nodes and a full standard credit spread terms, respectively.

As an interesting test, we also tested the choice of a term structure of the indexes. For each series of indexes, we assume a credit spread curve with 3y, 5y, 7y, and 10y nodes. Then, a stepwise linear hazard rate curve is built via a standard bootstrapping method. Note that this choice is fundamentally different from the ones with flat credit spread curves. In this model, the default probability for the 5y term is conditional on the market quote of the 3y term. 

