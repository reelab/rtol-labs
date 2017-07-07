
# Lab 6: Molecular Clocks, Fossils, and Lineage Through Time Plots

<img src="http://rlv.zcache.com/ant_clock-r6c24217958bc4be0a46405d2d19fb782_fup1s_8byvr_324.jpg" style="float: left; width: 35%; margin-left: 1%"><img src="https://www.antweb.org/images/jv-ad9/jv-ad9_p_1_high.jpg" style="float: left; width: 49%; margin-right: 1%; margin-bottom: 4%"><p style="clear: both;">

Congratulations on making it to the final lab! Today, you will use the tree from last lab and infer the actual dates of divergence of the taxa in the tree. By the end of the lab, you will be able to:

1. Infer divergence dates on a phylogeny using fossil calibrations.
2. Produce a Lineages Through Time plot.

## BEAST: Bayesian Evolutionary Analysis by Sampling Trees

_BEAST_ is a cross-platform program for Bayesian MCMC analysis of molecular sequences. It is entirely orientated towards rooted, time-measured phylogenies inferred using strict or relaxed molecular clock models. It can be used as a method of reconstructing phylogenies but is also a framework for testing evolutionary hypotheses without conditioning on a single tree topology. _BEAST_ uses MCMC to average over tree space, so that each tree is weighted proportional to its posterior probability. The program can do a lot, but today you will use it simply to infer a phylogeny and simultaneously estimate divergence dates.

Part of _BEAST_'s extensibility is due to its use of xml files as input - however, this also makes formatting the infile for _BEAST_ rather difficult! So, the program _BEAUTi_ was created to assist in generating the xml file, and also to show the world that phylogeneticists have a sense of humor. Today, you will use _BEAUTi_ and the _BEAST_.

1. Open up _BEAUTi_ and load in the sequence file ("LWRh_wg.nex", off the course website per usual), by going to File>Import Data.
2. A short block was added to the end of the file with information on the partitions, which you can see now in _BEAUTi_.
3. Now, you want to add fossil calibration data! But first you have to make taxon sets. Go to the "Taxa" tab and click on the little "+" button at the bottom left.
4. Choose all of the _Tetraponera_ species. You can choose multiple species at a time using command-click or shift-click.
5. Once they are all selected, click on the green arrow to move them to the "Included Taxa" box.
6. Rename this Taxon Set by double-clicking on "untitled1". Name it "Tetraponera".
7. Click on the "Mono?" box to constrain the topology to make _Tetraponera_ monophyletic.
8. Now repeat the process by again clicking on the "+" and making a _Pseudomyrmex_ taxon set.

Remember how we discussed in lecture how concatenating different genes and inferring phylogenies with one model of evolution for all genes is not the best? _BEAST_ makes it very easy to uncouple substitution models for different genes:

1. Go back to the "Partitions" tab and select the two gene partitions.
2. Click the "Unlink Subst. Models" button.
3. Now you can see that there are two models under the "Site Model" header (these may have been different already, but it's good to know how to unlink the substitution models!).
4. The label for the other models is automatically called "LWRh", but that's ok! You can just leaving these models as linked for now.
5. Now go to the "Sites" tab.
6. You have to specify the model for each partition, which you can determine the same way as before in a previous lab: Go to the web server [_FindModel_](http://www.hiv.lanl.gov/content/sequence/findmodel/findmodel.html?sample_input=1), and test each gene for the best model (using "LWRh.afa" and "wg.afa" from the course website). Remember to click "Reset" before uploading each file!

**Question:**
Which model is best for each gene?

1. Go back to BEAUTi and specify the correct model for each gene. Look in the left pane to see which gene is currently selected.
2. For the GTR plus gamma model, you can set "Gamma" under "Site Heterogeneity Model". "Base frequencies" can be left at "Estimated".
3. For the K80 model, you specify a "HKY" substitution model, "All equal" base frequencies, and a "Gamma" site heterogeneity model.
4. Make sure you set the right model for each gene!
5. Now go to the "Clocks" tab, and change the model to "Lognormal Relaxed Clock (Uncorrelated)". Also check the "Estimate" box.
6. Now click the "Trees" tab.
7. Choose "Speciation: Yule Process" for the "Tree Prior". See [here](http://beast.bio.ed.ac.uk/tree-priors-and-dating) for some information on tree priors and dating.

Now it's finally time to set fossil calibration priors. Remember that _BEAST_ uses Bayesian inference, which is all about setting priors. You have some fossil information that provides real time estimates of the age of some nodes that can help you infer more accurate ages for those nodes as well as the rest of the nodes in the tree).

##### Some information on fossil calibration
You have information for two fossils: a _Tetraponera_ fossil that is 44.1 million years old, and a _Pseudomyrmex_ fossil that is 15-20 million years old. However, one great thing in _BEAST_ is that you don't have to fix nodes to these ages, but can specify a _distribution_ of ages with an absolute minimum date set to the age of the fossil. A common distribution to use is the lognormal distribution. This distribution sets the mean possible age close to, but somewhat older than, your fossil age.

**Question:** 
- Why does it make sense to set the minimum possible age to the age of the fossil?
- Why do you think the lognormal distribution is a reasonable distribution to use in this context?

1. Go back to _BEAUTi_ and select the "Priors" tab. 
2. Click on the button in the "Prior" category next to the "tmrca(Tetraponera)" parameter.
3. Select the appropriate distribution.
4. Set the "Initial value" to "0" and the "Offset" to the age of your _Tetraponera_ fossil, **in units of millions of years**. Then click "OK".
5. Now do the same thing for the _Pseudomyrmex_ node, using the age from the _Pseudomyrmex_ fossil.
6. Lastly, set the "ucld.mean" parameter (the mean rate of molecular change across all of the branches) to an initial value of "5.3E-4" (this is $5.3 * 10^{-4}$) under a uniform distribution, with lower and upper bounds set to "0" and "1", respectively.

Lastly, you have to set the parameters for the MCMC run.

1. Click on the "MCMC" tab.
2. Specify the length of the chain to "500000", and log parameters every 1000 steps.
3. Change the output file names if you want, but you don't have to.
4. If the box is checked, **uncheck** the box for "Perform marginal likelihood estimation", unless you really love these labs and want to be here until Saturday morning.
4. Now click the "Generate BEAST file" in the bottom-right, then click "Continue", then "Save".
5. Look at the xml file in a _TextEdit_. Hopefully you can see why using _BEAUTi_ is better than manually creating the file!

Now finally you get to run _BEAST_! **Open _BEAST_, choose your xml file, and hit "Run".**

You should start seeing output a lot like the output from _MyBayes_, showing the parameters as the chain progresses. There are columns for the "Posterior", "Prior", "Likelihood", etc. This should take under a minute to run.

There will be two output files, one ending in "log" and one ending in "trees". The log file is the log of all the estimated parameters and likelihoods and the trees file stores all of the inferred phylogenies from the posterior distribution. First you will use the program _Tracer_, which you've used before, to make sure that you have run our chain for long enough.

1. Open _Tracer_ and load in the log file.
2. Specify burn-in in the top left - there are 500,00 states in the analysis, so if you want to use 10% burn-in you should specify 50,000. Although this should already be set to a burn-in of 10%.
3. Look at the distribution of the parameters and the ESS values. Remember, you want ESS values to be above 100 for adequate convergence.

**Question:** Has the chain run long enough? How do you know?

There are also two files on the course website, a log file and a tree file, for a run of 100 million generations, sampling every 100,000. **Look at the log file for that run and compare to the run you just did).**

**Question:** Which run is better? Was 100 million generations long enough?

Now we will make a single tree from our distribution of trees, called the "maximum clade credibility tree", using the **tree file** from **the run with 100 million generations**.

1. Open the program _TreeAnnotator_, another program in the _BEAST_ package.
2. We have 1,000 trees, so specify a burn-in of 100 _as the number of trees_.
3. Also specify the "Posterior probability limit" - 0.5 (50%) should be fine. This means the program will only consider topologies where the relationship was found in 50% or more of the trees from the posterior distribution of trees.
4. Choose the "100million.trees" file as input, and provide a name for the output file using a ".nex" extension.
5. Click "Run".
6. Open the output file in _FigTree_, and choose "Node ages" for the "Node Labels", "Height_95%_HPD" for the "Node Bars", and show a "Scale Axis" with the "reverse axis" option on.

**Questions:**
1. What do you notice about the confidence intervals around these divergence dates?
2. What is the inferred crown age for all these taxa?
3. What is the inferred crown age for _Psuedomyrmex_?

Repeat the steps above for your original _BEAST_ run with 500,000 generations. Now you just have 500 trees, so specify a burn-in of 50 trees.

**Question:** Do you notice any significant differences between these two runs? Either way, why do you think this might be?

## Lineages Through Time Plots

Now that you have a dated tree (chronogram), you can do all kinds of diversification analyses. Today, you will just do one simple analysis called the "lineages through time plot" (LTT plot). As you may remember from lecture, this plot shows the accumulation of lineages, i.e. branches, on your tree as you move towards the present. There is an expectation that lineage accumulation will tend to appear exponential, so the y-axis (number of lineages) is usually set to a logarithmic scale. Then, you can see if there are any deviations from a linear relationship throughout the evolution of your taxon of interest and associate these apparent changes in diversification with known factors (e.g. historical changes in the environment).

You can do this easily using your BFF _R_:

1. Open R (or RStudio).
2. Load the package "ape", like you did before.
```r
library(ape)
```
3. Set the working directory to wherever your tree file is, using `setwd()`.
4. Load your tree from the 100 million generations run (using the actual file name you gave your maximum clade credibility file):
```r
tree <- read.nexus("MaxCladeCredTree.nex")
```
5. Now it's very simple to produce an LTT plot:
```r
ltt.plot(tree)
```
6. Voila! But remember that you want the y-axis to be on a logarithmic:
```r
ltt.plot(tree, log = "y")
```
7. Voila for real!

**Questions:** Based on the LTT plot, is there evidence for shifts in diversification? Why or why not? Remember that the null expectation is that the accumulation of lineages appear linear.

## Congratulations!

Congratulations on completing all 6 labs! You win a gold starfish.

<img src="http://previews.123rf.com/images/nikkytok/nikkytok1106/nikkytok110600139/9833621-starfish-or-sea-star-on-the-sand-Stock-Photo.jpg" width="500" height="10">

---
#### Warning!
If you leave your final project to the last minute, **you will lose your gold starfish**.

---

### Email me:

1. Completed .ipynb Lab file (include your name(s) in the file name)
2. Output .log and .trees file from your initial _BEAST_ run.
