
# Lab 4: Model Selection & Bayesian Inference

<img src="http://i.imgur.com/b0InxJ4.jpg" width="800" height="10">

Today we will evaluate the fit of a number of substitution models (models of evolution) to our data using the Akaike Information Criterion (AIC). Once we decide on an appropriate model, we will run a Bayesian analysis (specifying this substitution model) to search "tree space" and obtain a large sample of trees. We will then construct a majority-rule consensus tree of these sampled trees.

By the end of lab today, you will know how to:

1. Evaluate substitution models and select one for your data set.
2. Perform a Bayesian analysis.
3. Evaluate some post-run statistics from our Bayesian analysis.

## Model Selection

A variety of statistics can be used to select a substitution model, including the likelihood ratio test (LRT), Akaike Information Criterion (AIC) and Bayesian Information Criterion (BIC). Typically, people use _jModelTest_ or _MrModeltest_ to perform these analyses and calculate these statistics - we will use a program called _FindModel_, which is similar to these.

When estimating the likelihood, you might find that more parameter-rich models give better likelihoods. However, more complex models are more computationally difficult to analyze, and as more parameters are estimated, more error can be introduced. So the goal is to find a balance - the model should have enough parameters to explain the data, but not too many! Today, we will just be using AIC, but see the end of the lab for a description of LRT and BIC!

##### Akaike Information Criterion (AIC)

The AIC is calculated by multiplying the log-likelihood of the hypothesis under that model by -2, and then adding twice the number of free parameters included in that model (this is a penalty assessed for the number of free parameters in the model). This is done for each model used in the comparison, and the model with the lowest AIC value is the one selected.

$AIC_i = -2lnL_i + 2p_i$

This is probably the most commonly used statistic for model selection. Unlike the LRT, it does not assume models are nested.

##### AIC Test

_FindModel_ is a webserver that runs an analysis similar to _MrModeltest_, and is available [here](http://www.hiv.lanl.gov/content/sequence/findmodel/findmodel.html?sample_input=1). It first rapidly constructs a tree (using one of several programs), to give us an approximation of topology and branch lengths. Then the likelihood of the data is evaluated for this tree under different models of evolution. After the likelihoods are estimated, the models are compared using AIC to evaluate the fit of the model to the data.

1. Hit the "Reset" button (below the Input and Options sections).
2. Upload your "LWRh_wg.afa" file.
3. You don't need to change the option for initial tree construction, but you can if you want.
4. Leave the box for "Use all 28 models" **unchecked** (it will run faster this way).
5. Run the test! It will take approximately 3 minutes.
6. **Question:** Which model is selected by the AIC?

## Bayesian Inference With MrBayes - the NEXUS FILE and Executing a Run

<img src="http://77.235.253.122/tools/mrbayes/mrbayes_logo.png" width="400" height="10">

_MrBayes_ uses NEXUS files. These can be fairly simple files that always begin with "#NEXUS" and are followed by a block or several blocks of information.

We can convert our .afa file to NEXUS format using a number of applications (including our good friend _Mesquite_), but for now we'll just use a file converting web server [here](http://www.ibi.vu.nl/programs/convertalignwww/). 

1. Upload your sequence file and convert it to NEXUS format.
2. Open TextEdit, go to "Format", and select "Make Plain Text".
3. Copy the text from the web server output and paste them into the text file.

Now, we still need to make a few modifications to this file for it to be read by _MrBayes_:

1. Line 6 of the file looks like this: 
"format interleave datatype=dna gap=- symbols="ATCNG";"
2. Delete the "symbols" field, so the line now looks like this: "format interleave datatype=dna gap=-;"
3. _MrBayes_ cannot handle single quotes (') so find and replace all of them with nothing (using "Find" under "Edit")

Several commands need to be entered into _MrBayes_, but these can be compiled into a single "Bayes block" in the NEXUS file. This is placed at the end of the file. At the end of your file, you should see "endblock;". On the next lines, type the following (**no spaces** between the lines in the actual text file):

---
BEGIN MRBAYES;

prset statefreqpr=fixed(equal);

lset nst=2 rates=gamma;

mcmcp savebrlens=yes ngen=10000 samplefreq=10 printfreq=10 nchains=4
temp=0.2 relburnin=yes burninfrac=0.25;

END;

---

Now **save** the file, with the extension ".nex" (a friend of mine recently said that she likes the name "LWRh_wg_bayes.nex")

|Parameter|Description|
|:---------:|:----------:|
|prset|Sets the "priors". We're leaving most at the default, but we set the model of evolution selected from the AIC. The Kimura 2-parameter plus gamma model has two rate parameters for transitions and transversions and assumes that all base frequences ("statefreqpr") are equal.|
|lset|Sets model parameter values. "nst" sets the number of substitution rate categories (2), and "rates" specifies what statistical distribution the across-site rate variation corresponds to (gamma).|
|mcmcp|Sets the Markov Chain Monte Carlo parameters. "savebrlens=yes" tells the program to save branch lengths. "ngen" sets the number of generations for the MCMC. "samplefreq" indicates how often to sample the MCMC chain. "printfreq" tells the program how often to display results. "nchains" sets the number of independent chains for running the MCMC. "temp" tells the program what "temperature" to set the chains. "relburnin=yes" means that you will discard a set number or proportion of trees as "burn-in". "burninfrac" indicates what fraction of trees is to be discarded as burn-in.|

Now, in order to run _MrBayes_, use command-spacebar and search for the folder _MrBayes_. Double-click on "mb" to open _MrBayes_.

1. Type "execute" followed by the name of your file (with the file path - you can drag the file into Terminal to past the file path, like you did with _PHYLIP_). Hit enter.
2. Enter "showmodel" to have _MrBayes_ print the currently specified model used for estimating the phylogeny.
3. Once you are ready to begin the analysis, type "mcmc" and hit enter. The analysis begins!!! Ideally.

Here's some light reading while the analysis runs:

The program will create files in the folder with your input file. Since we have two parallel runs, two parameter files (ending in .p) and two tree files (ending in .t) will be created - one for each run. It will also list the values and settings for the run and you will then see a series of generations with a number of likelihood scores. These are the likelihood values for each chain at specific generations. You may also notice the "cold chains" [in brackets] and the heated chains (in parentheses). Trees and parameters in the "cold chain" are stored in the tree and parameter files, while the "heated chains" serve to explore space and act as "scouts" for the cold chain. Periodically, switches between chains will be attempted, and if a successful switch is made between a cold and heated chain, that heated chain will become the cold chain.

Let _MrBayes_ run for 10,000 generations - it will ask you if you are ready to stop. You don't really want to stop until the average standard deviation of split frequencies is below 0.01, but for time reasons just tell it to stop when it reaches 10,000 generations.

## Post-Run Summaries and Analyses from MrBayes

A number of post-run summaries and statistics can be useful to evaluate the convergence and mixing of runs. Basically, it helps you determine if independent runs have converged on more or less the same solution. Two sources of information that can help you evaluate convergence are printed immediately at the end of the run: 

###### Average standard deviation (SD) of split frequencies

This measures the SD between the two parallel runs. You want a low number (less than 0.01), which indicates that each run converged on the similar group of trees.

###### Proportion of successful state exchanges
This allows you to examine chain swap information for each run. These values report the proportion of times the chains have successfully swapped, and can be a good measure of "mixing". Good mixing means that the chains have all converged on a similar solution. You want values in the upper-right diagonals to be between 0.1 and 0.7.

---

**Question:** How do these values look? Do you think you should run the chain for longer? Why or why not?

---

After these values have been checked, you can summarize more of the data from the runs in _MrBayes_. You want to include samples from the latter part of the search, because a MCMC chain usually starts out in a space with poor likelihood and gets better over time. To do this:

1. Enter "sump burnin=251" into _MrBayes_.

This tells _MrBayes_ to produce a summary, but discard 251 trees as burn-in (because we want to discard about 25% of the 1001 trees sampled per run).

If you scroll up a bit in Terminal, You will notice a figure with a bunch of 1's and 2's. This illustrates the likelihoods through time (generations) for run 1 and 2. You want the 1's and 2's to be mixed up - otherwise, it likely means that the sampled trees have not converged on a solution, and you would normally want to run the chain for longer.


---
**Questions**

1. What do the axes on this graph represent?

2. Has the chain been run long enough? Why or why not?

---

You will also notice a table with a variety of values for each parameter, along with the Potential Scale Reduction Factor (PSRF) for each parameter (you can expand the width of the Terminal window if you want). The PSRF is a rough convergence diagnostic (rough because a lot of assumptions are violated). These values should be near 1.0 if the runs have converged.

---
**Question**: What do the PSRF values for your run indicate about the length of your run?

---

Now we will use the "sumt" command to build a majority-rule consensus tree from all the post-burnin samples (i.e. discarding the first 251 trees), using the trees from both runs.

1. Enter "sumt burnin=251"

You will see a variety of statistics related to taxon bipartitions, then your tree. The top tree shows posterior probabilities (but branch lengths are irrelevant), while the bottom tree displays branch lengths. The numbers reported on the tree are NOT bootstrap values, but are measures of clade support called posterior probabilities. These represent the proportion of sampled trees containing these clades. Typically, a value of 95% or 0.95 is considered evidence for strong support.

1. Rename the file with the ".con.tre" extension to something else (but keep the .con.tre extension).

2. Visualize this consensus tree in _FigTree_.

3. Add the posterior probability scores to the tree by checking the "branch labels" box, and selecting "prob".

---
**Question:** Describe the support for the tree topology you've inferred with _MrBayes_. Do most clades have high support? Is the level of support consistent across the tree?

---

The "sumt" command creates a majority rule consensus tree by default, but you may not care about confidence in your topology and just want to have a well-resolved tree. You can tell _MrBayes_ to make a majority rule consensus tree but add all groups of taxa that are compatible with that tree, which will resolve polytomies at the cost of low confidence in the tree.

1. Enter "sumt burnin=251 contype=allcompat" (it will ask you if you want to overwrite previous tree sample summaries, and you can say "yes" **as long as you renamed your previous tree file**).

2. Rename this tree as well (again keeping the ".con.tre" extension.

Now look at this tree.

We know that we did not let _MrBayes_ run long enough to converge on the best tree, but:

**Questions:** 

- How do these trees compare to the tree you made last week using maximum likelihood (bipartitions file)? You can get this tree from the email you sent to me last week, if needed (if you're using the same computer, it may still be in the directory you used last week).

- How does the topology of your trees compare to the topology in the original paper where we got the data ([here](http://www.sciencedirect.com/science/article/pii/S1055790310001168))? If it differs, why do you think this might be?

### Tracer (if there is time!)

<img src="http://tree.bio.ed.ac.uk/software/figtree/icon.png" style="float: left; width: 15%; margin-right: 5%; margin-left: 8%; margin-top: 1.5%"><img src="http://tree.bio.ed.ac.uk/software/figtree/icon.png" style="float: left; width: 15%; margin-right: 5%; margin-left: 5%;"><img src="http://tree.bio.ed.ac.uk/software/figtree/icon.png" style="float: left; width: 15%; margin-right: 5%; margin-left: 5%;"><p style="clear: both;">

_Tracer_ is another useful program for determining if the chain has run long enough.

1. Open up _Tracer_.

2. Load one or both of the output files (ending in ".p") from your original _MrBayes_ run using the "+" button.

3. Specify the burn-in by double-clicking on the correct cell in the matrix in the top-left of the application window. _Tracer_ looks at ALL 10,000 iterations that you ran today, so specify "2501" for burn-in.

4. Look at the plots in the "Estimates" and "Trace" tabs at the top (for the "combined" dataset if you loaded both ".p" files), as well as the "Effective Sample Size" (ESS) values in the table.

---
**Questions:**

1. What do you think the ESS values in red indicate?

***without further info, is this a useful question???***

2. What is your conclusion about the length of your run based on these results in _Tracer_? Why?

___

If you still have some time, download the zip file from the course website and load in the ".p" files from a chain run for 10,000,000 generations.

**Question:** How does this compare? Has this chain been run long enough?

### CIPRES

Like RAxML, you can also run _MrBayes_ on the [*CIPRES* computing cluster](https://www.phylo.org/portal2/login!input.action). You will definitely want to do this if you have a lot of sequences, as analyses can sometimes take days!

## Email Me (bblanchard@uchicago.edu)

1. Completed lab file (in .ipynb format, w/your name(s) in the file name).

2. The two tree files from the _MrBayes_ analyses (ending in ".con.tre").
