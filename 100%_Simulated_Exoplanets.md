# Simulating an Exoplanet Population to Get a Drift Rate Distribution

In order to make a 100%-simulated exoplanet population for drift rates, we will need to correct for observational biases. Sofia came up with a procedure for this originally that... it turned out was probably the wrong way to approach the problem. After consulting with Dr. Mariah MacDonald, I have a new plan.

1) Move existing distribution-functions into a new notebook: longitude of ascending node, argument of periapse, and inclination
2) Implement new distribution-functions for eccentricity, stellar mass, and semi-major axis/period based on the information from the SysSym project papers (group includes Danley Hsu, Matthias He, Eric Ford, and Darin Ragozzine).
- Eccentricity: ??? (REFERENCE?)
- Stellar Mass: To figure out the distribution of stellar masses in the galaxy, we use something called an "Initial Mass Function" (or IMF). The most famous IMF was the first one, the Salpeter IMF from like 1951 or something. Apparently, the most up-to-date version that people use is the Kroupa IMF (https://academic.oup.com/mnras/article/322/2/231/962260), so that's what we can go with. Look at Section 2.2 (Equation 2) for what the "Kroupa multiple-part power-law IMF" is. Let me know if you have questions implementing or deciphering that paper.
- Semimajor Axis/Period: ??? (REFERENCE?)
3) Independently (SOFIA: am checking if this is valid) draw a few thousand values from each distribution and combine them into fake exoplanet rows.
4) Get their drift rates with your existing code.
5) Profit!!!

OLD PROCEDURE:
~~1) What are the major surveys that provide the data that we're using (try to get, say 90% of known exoplanets)? ~~
~~2) Collect data (histograms in each parameter) for each of those surveys~~
~~3) Find papers for each parameter that tell us what corrections to apply~~
~~4) Add simulated values to the histograms to un-bias them~~
~~5) Stack them together~~
~~6) Make them into distributions that we can draw from using Kernel Density Estimation (KDE)~~
~~7) Draw thousands of planets from the corrected distributions~~
~~8) Profit!~~

# Old info

## Adjusting the Existing Exoplanet Population to Get a Drift Rate Distribution

To get from the subset of a few 100 planets to the full ~4000 exoplanets in the NEA, we need to account for the missing values in the NEA. From Megan's investigative work, we know that (for all 29000 rows)... **[Sofia will update this for the ~4000 planet sample once Megan has gotten those values]**

1) Null Longitude of Ascending Node = ~100% - add by drawing from a uniform distribution across all angles - DONE
2) Null Argument of Periapse = ~90% - add by drawing from a uniform distribution across all angles
3) Null Inclination = ~50% - add by drawing from a uniform distribution across all angles
4) Null Eccentricity = ~50% - add by drawing from a Beta distribution with  a = 0.867 and b = 3.03 (Kipping 2013, from ~400 RV planets)
5) Null Stellar Mass = ~15% - add by... ???
6) Null Period = ~10% - add by... ???
7) Null Semimajor Axis = 45% - add by... ???

Questions for Howard: 

- Why are the period and semimajor axis numbers so different? ANSWER: That's weird, let's check back in once we have used the ~4000 planet sample
- For exoplanets that are null in only 1 of period, stellar mass, and semimajor axis, would it be valid to use the stellar mass + period to get the semimajor axis (or vice versa)? ANSWER: Probably, but also, we would expect the papers to have done this already: check back in later
- **How do we deal with exoplanet parameters that are not independent e.g., eccentricity and distance (Kipping 2013), or eccentricity and planet size (Kane et al. 2012)?** ANSWER: Howard and Sofia will think about this more - don't worry about it yet!

### To-Dos
- Fix up the TRAPPIST plots: color-code the lines, fix up the x-axis, color-code (same colors) the histogram
- Download the new ~4000 planet sample
- Figure out null numbers for each parameter in the ~4000 confirmed planet sample - make sure that the semimajor and period percentages are low (<5%). If not, we'll investigate.
- Re-do 2 histograms with the ~4000 planet sample (only planets without LoAN, then add planets without LoAn, AoP, or inclination)
- Write a function that inputs a histogram of drift rates and a % of values included, and figures out the drift rate limits (-nu_dot to nu_dot) which encompass that percentage of the histogram - put that information on the plot
- Use a pie chart on the ~4000 confirmed planets to see which are the major methods and also facilities (two separate columns, two separate pie charts)
- Sofia will investigate the eccentricity distribution for transiting planets
