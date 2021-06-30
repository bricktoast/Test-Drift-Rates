## Using the NASA Exoplanet Archive (NEA) to Simulate Drift Rates

Now that we're able to produce drift rate histograms for individual systems, it's time to try expanding that to a population of known systems. There are three potential issues that I see with this plan:

1) The geometric parameters given in the NEA are not the same as the ones that we were using in the example.
2) Exoplanets in the archive do not have all of the parameters that we need to fully describe the system.
3) Exoplanets in the archive are inherently selection-biased because of the particular exoplanet discovery methods that we use.

Let's address those issues one at a time.

### Which Parameters Do We Need for the Code vs. Which Parameters Does NEA Use?

For the code that we have so far we need:
- semimajor axis (AU)
- period (years)
- eccentricity
- inclination (degrees)
- argument of periapse (degrees)
- longitude of ascending node (degrees) 

The NEA provides:
- semimajor axis (AU)
- period (days)
- eccentricity
- inclination (degrees)
- argument of periastron (degrees)

So it provides everything we need *except* longitude of ascending node. Why is that missing? Is there another angle that provides the same information, or is it just unmeasured/unmeasurable for some reason?

NEA's documentation suggests that the argument of periastron is "the angle between the planet's ascending node and its [periapse]", which lines up with PyAstronomy. *And* there are no other provided orbital angles. So it seems like the information is just not included.

To investigate this, I checked out [exoplanets.org](exoplanets.org), which was last updated on June 21, 2019 (so it's now out of date). This database *does* include the longitude of ascending node as a column... but only 4 of 3236 exoplanets have a known longitude of ascending node. So it turns out it's just a really difficult parameter to measure (Howard, any intuition on why this is?). This brings us to the next section:

### What do we do if the NEA is missing a parameter that we need?

So what do we do about every system missing a longitude of ascending node? Well, if exoplanetary systems are randomly oriented in the sky (which they should be for a randomly-placed observer aka. Earth), then in a large sample, the longitudes of ascending node should be uniformly distributed from Ω=[0,360] degrees. So for each system, we can randomly draw a value in that range and use it as the longitude of ascending node! 

Similarly, to start, we can [go to the NEA](https://exoplanetarchive.ipac.caltech.edu/), click on the "Confirmed Planets" button, and look for systems in the table that have all 5 of the parameters listed in the section above (no blanks). For each parameter, you can type "not null" into the search box of the column to filter out blanks. This will leave you with some subset of systems that have all of the information we need, and I bet it will be a small subset. We can start with that subset, but we'll eventually want to use our knowledge of distributions to fill in the blanks, just like with the longitude of ascending node example.

- **Semimajor Axis and Period**: These are critical - if the planet doesn't have this information, I don't think we can use it. We will return to the idea of selecting a semimajor axis/period when we simulate beyond the known population of exoplanets later in the project.
- **Eccentricity**: We know that the values for bound planets must be between [0,1]. We can probably find a paper on the eccentricity distribution of exoplanets (any idea Howard?) - generally, because tides and other physical processes cause orbits to circularize over time, I would expect the orbit to be peaked at 0 and taper off towards 1.
- **Inclination**: Planet detection methods have their own biases (transit and RV are much more sensitive to inclinations of +-90 degrees), but just as with the longitude of ascending node, we should expect inclinations to be uniformly distributed between i = [-90, 90].
- **Argument of Periastron**: Just as with longitude of ascending node and inclination, the underlying distribution here should be uniform between ω = [0,360]. 

(Howard, want to talk about how to implement Monte Carlo methods here?)

### How do we account for the inherent selection biases in the methods?

Short answer: we will take the existing planet distributions in each parameter for each method, correct them for biases, lump them back together, and draw randomly from the new corrected distribution to create thousands of simulated systems. Then we'll measure drift rates for all of those systems. This will be a "later in the project" task, but hopefully this week's tasks will help you understand how we might implement this later on.


## To-Do: 06/28-07/06
- Figure out how many "Confirmed" planets there are in the NEA. Now what fraction of those have non-null values for all five necessary orbital parameters? 
- Download the table as a .csv for all of these "all-non-null" planets. You'll probably want all 5 orbital parameters mentioned above (with their errors and limit flag columns!), plus the planet name, number of stars and planets in the system, the discovery method, the stellar mass, perhaps RA and DEC, and any other information that you think might be useful for understanding the data later on (maybe planet mass?).
- Write a function to randomly select a value from a distribution - at first, you'll use it for selecting a longitude of ascending node in the next step, but you'll want to make it general so you can use it for other parameters later.
- Write a wrapper for your drift rate function that lets you: a) iterate through each planet in the all-non-null planets list, b) assign them a random longitude of ascending node, c) sum their drift rate arrays together*, and d) visualize the distribution of all of their drift rates in a histogram. You may need to try both logarithmic and linear scalings for your histogram bar heights [This may be as far as you get this week, and that's fine!]
- We'll want to explore the dataset a little bit: now download the NEA's full confirmed planets .csv with all of the columns that you selected for the all-non-null table. How many of them (percent of total) are missing each of the five necessary parameters?

Based on that last point, and also what we learn about the distribution of eccentricity of exoplanets, we'll want to repeat some random drawing of parameters with the new, full population and see how the histogram changes. 

*When you choose your values for an exoplanet's drift rate calculations, for now, use the "nominal" value, which is the one in the primary column for that parameter. For example, 7 CMa c has a nominal stellar mass of 1.34 solar masses. But with the other error columns, we can see that this value is actually likely somewhere in the range of [1.34-0.12,1.34+0.11] solar masses, because of the error bars on the measurement. To account for measurement errors like this, we would want to produce a split-normal distribution with variances given by the values in those error columns, and draw a value from that, instead of just using the nominal value. This is trickier for upper or lower limits instead of ranges, but we can discuss that in the future.
