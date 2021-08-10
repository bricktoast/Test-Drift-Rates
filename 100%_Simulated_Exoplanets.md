# Simulating an Exoplanet Population to Get a Drift Rate Distribution

In order to make a 100%-simulated exoplanet population for drift rates, we will need to correct for observational biases. Sofia came up with a procedure for this originally that... it turned out was probably the wrong way to approach the problem. After consulting with Dr. Mariah MacDonald and Matthias He, I have a new plan.

1) Move existing distribution-functions into a new notebook: longitude of ascending node, argument of periapse, and inclination
2) Implement new distribution-functions for eccentricity, stellar mass, and semi-major axis/period based on the information from the SysSym project papers (group includes Danley Hsu, Matthias He, Eric Ford, and Darin Ragozzine). This is the bulk of the work. See sections below:

## Eccentricity
Turns out the Kipping paper with the beta distribution is a bit outdated, so we should move to using one of the SysSim papers. The most up-to-date eccentricity distribution is from Section 3.5 of [He, Ford, and Ragozzine 2019](https://arxiv.org/pdf/1907.07773v2.pdf). This study was done by extrapolating from Kepler data, which means that it's probably underrepresenting giant planets, which may have a different distribution. Unfortunately, there's very little work on giant planet eccentricity distributions: as we get more TESS data, that will change. The other thing to mention is that eccentricity is related to planet multiplicity and semimajor axis: planets in multiplanet systems are more likely to be circular (for stability), as are planets that are very close to their star (tidal circularization). We're going to neglect those complexities for now. All of this is perfectly okay for our purposes, just something to mention in the Methods/Future Work sections. The equation to use, based on He, Ford, and Ragozzine 2019, is a [Rayleigh distribution](https://en.wikipedia.org/wiki/Rayleigh_distribution), with sigma_e = 0.02. The sigma_e parameter is called "scale" in the [NumPy random variable-drawing implementation](https://numpy.org/doc/stable/reference/random/generated/numpy.random.rayleigh.html).

## Stellar Mass
To figure out the distribution of stellar masses in the galaxy, we use something called an "Initial Mass Function" (or IMF). The most famous IMF was the first one, the Salpeter IMF from like 1951 or something. Apparently, the most up-to-date version that people use is the Kroupa IMF [(original paper)](https://academic.oup.com/mnras/article/322/2/231/962260), so that's what we can go with. The [Wikipedia article](https://en.wikipedia.org/wiki/Initial_mass_function#Kroupa_(2001)) gives a good definition for it; it's something called a "broken power law", which consists of multiple different power laws depending on where you are in the x (mass) axis. Remember that bodies smaller than 0.08 Solar Masses don't burn hydrogen, and are brown dwarfs, not stars. So we can make 0.08 Solar Masses our lower bound, with an upper bound of ~150 Solar Masses (should probably find a definitive reference for this value for the paper). With a little tweaking, you should be able to use the [NumPy random variable-drawing implementation for a power-law](https://numpy.org/doc/stable/reference/random/generated/numpy.random.power.html) to make this work.

## Semimajor Axis/Period 
The best understanding right now is that the semimajor-axis / period distribution is best modeled as a broken power law, just like the stellar mass, with the break at a period of about 10 days. In [He, Ford, and Ragozzine 2019](https://arxiv.org/pdf/1907.07773v2.pdf), they simplify this by using a single power-law between 3 and 300 days. For our purposes, the simplified version should be good enough, and we can use the same NumPy implementation as in the Stellar Mass section. So the minimum and maximum bounds are 3 and 300, and the "alpha" parameter for the model is 0.2.

3) Once these three distributions are implemented, independently draw a few thousand values from each distribution and combine them into fake exoplanet rows. Because the drift rates produced by one planet in a system won't be affected by the other planets in the system, we can get away with pretending that each system is only a one-planet, one-star affair. This is definitely an assumption, but if we make it, then "drawing those values [semimajor axis, eccentricity, etc.] independently is a tolerable assumption" (quoting Matthias He here). Trying to de-bias the distribution of planetary _systems_ is a giant task, Matthias's whole PhD thesis, and nowhere near a solved problem. So sidestepping that problem entirely by only assuming one-planet systems is a good idea!
4)  Get the drift rate histogram with your existing code.
5) Profit!!!

# Old info

OLD PROCEDURE:
~~1) What are the major surveys that provide the data that we're using (try to get, say 90% of known exoplanets)? ~~
~~2) Collect data (histograms in each parameter) for each of those surveys~~
~~3) Find papers for each parameter that tell us what corrections to apply~~
~~4) Add simulated values to the histograms to un-bias them~~
~~5) Stack them together~~
~~6) Make them into distributions that we can draw from using Kernel Density Estimation (KDE)~~
~~7) Draw thousands of planets from the corrected distributions~~
~~8) Profit!~~

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
