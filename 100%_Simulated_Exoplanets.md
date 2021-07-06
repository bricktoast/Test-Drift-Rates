# Adjusting the Existing Exoplanet Population to Get a Drift Rate Distribution

To get from the subset of a few 100 planets to the full ~4000 exoplanets in the NEA, we need to account for the missing values in the NEA. From Megan's investigative work, we know that (for all confirmed/candidates/OIs)...

1) Null Longitude of Ascending Node = ~100% - add by drawing from a uniform distribution across all angles - DONE
2) Null Argument of Periapse = ~90% - add by drawing from a uniform distribution across all angles
3) Null Inclination = ~50% - add by drawing from a uniform distribution across all angles
4) Null Eccentricity = ~50% - add by drawing from a Beta distribution with  a = 0.867 and b = 3.03 (Kipping 2013, from ~400 RV planets)
5) Null Stellar Mass = ~15% - add by... ???
6) Null Period = ~10% - add by... ???
7) Null Semimajor Axis = 45% - add by... ???

Questions for Howard: 

- Why are the period and semimajor axis numbers so different?
- For exoplanets that are null in only 1 of period, stellar mass, and semimajor axis, would it be valid to use the stellar mass + period to get the semimajor axis (or vice versa)?
- **How do we deal with exoplanet parameters that are not independent e.g., eccentricity and distance (Kipping 2013), or eccentricity and planet size (Kane et al. 2012)?**

##To-Dos
- Write a function that inputs a histogram of drift rates and a % of values included, and figures out the drift rate limits (-nu_dot to nu_dot) which encompass that percentage of the histogram
- Figure out null numbers for each parameter in the ~4000 confirmed planet sample
- How many are null in only 1 of the following: period, stellar mass, and semimajor axis? How many are null in 2 or more? If the number of null in 1,2,3 of these parameters is low, remove them from the sample. Otherwise, try to 
- Use a pie chart on the ~4000 confirmed planets to see which are the major methods and also facilities (two separate columns, two separate pie charts)
- 

# Things for Later

## Simulating an Exoplanet Population to Get a Drift Rate Distribution

In order to make a 100%-simulated exoplanet population for drift rates, we will need to correct for observational biases:

1) What are the major surveys that provide the data that we're using (try to get, say 90% of known exoplanets)? 
2) Collect data (histograms in each parameter) for each of those surveys
3) Find papers for each parameter that tell us what corrections to apply
4) Add simulated values to the histograms to un-bias them
5) Stack them together
6) Make them into distributions that we can draw from using Kernel Density Estimation (KDE)
7) Draw thousands of planets from the corrected distributions
8) Profit!
