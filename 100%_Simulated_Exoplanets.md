# Adjusting the Existing Exoplanet Population to Get a Drift Rate Distribution



# Simulating an Exoplanet Population to Get a Drift Rate Distribution

In order to make a 100%-simulated exoplanet population for drift rates, we will need to correct for observational biases:

1) What are the major surveys that provide the data that we're using? (try to get, say 90% of known exoplanets)
2) Collect data (histograms in each parameter) for each of those surveys
3) Find papers for each parameter that tell us what corrections to apply
4) Add simulated values to the histograms to un-bias them
5) Stack them together
6) Make them into distributions that we can draw from using Kernel Density Estimation (KDE)
7) Draw thousands of planets from the corrected distributions
8) Profit!
