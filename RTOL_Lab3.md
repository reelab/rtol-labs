
# Lab 3: Maximum Parsimony, Maximum Likelihood, and Bootstrap

It's the day you've been waiting for - Tree Reconstruction Day! This is the day you learn how to infer tree topologies using the maximum parsimony and maximum likelihood optimality criteria. You will also perform bootstrap analyses to estimate the robustness of your inferred topologies.

#### By the end of the lab, you will know how to:
1. Reconstruct a tree using the Maximum Parsimony (MP) optimality criterion.
2. Perform a bootstrap analysis on the data set.
3. Reconstruct a tree using the Maximum Likelihood (ML) optimality criterion and perform a bootstrap analysis.

#### Prepare data files
We will use the sequences that we aligned last week to reconstruct the phylogenies today.
1. Open the aligned "LWRh_clean.afa" sequence file in _Mesquite_ like we did last week.
2. Rename the matrix by clicking the current name (the line that says "Character Matrix" on the left-hand side) - "LWRh" is a nice name.
3. There is an intron in the middle of this gene (position 241 to 339) that is difficult to align, so we will remove it. Highlight all of the intron characters for all sequences (select the first site, then shift-click the last site) and delete them using "Matrix>Delete Selected Chars or Taxa".
4. Now we will add another gene to our dataset. Go to "File>Include File" and select the aligned wingless gene file ("wg_aligned.afa").
5. Rename this character matrix as well ("Wg" is pretty peachy).
6. Return to the character matrix viewer for LWRh, then go to "Matrix>Utilities>Concatenate Other Matrix".
5. Select the matrix to concatenate (you should have only one option) and click "OK".
6. **Question:** How long is the concatenated matrix?
7. There should be two character matrices now, one with the combined data and with just a single gene - delete the single gene matrix taxa block (control-click taxa block name>"Delete Taxa Block"), and rename the concatenated matrix.
8. Now go to "File>Export", choose "Phylip (DNA/RNA)" format, and click "Export". Give the exported file an informative name (e.g. "LWRh_wg.phy").


---
##### Note: Concatenating Genes for Phylogenetic Analysis
Concatenating genes for phylogenetic analyses is common, but this practice can sometimes yield misleading results. Different gene histories can cause two important problems:
1) Differences in _topology_ between genes and phylogenies, and
2) differences in _models of evolution_ between genes.
This is good to keep in mind when you decide whether or not to concatenate genes! A way to partition your data to allow for different models of evolution for each gene is discussed at the end of the lab.

---



## Maximum Parsimony Using PHYLIP

***NOTE: change instructions to first copy the executables to working directory and save headaches with paths***

**_PHYLIP_: PHYlogeny Inference Package** is a free package (Max, Windows, and Unix/Linux) primarily geared towards evolutionary analyses.

---
##### PHYLIP Format
PHYLIP-formatted files begin with the number of taxa, followed by the number of characters, then the taxa names and sequences. **Taxon names must be 10 characters or fewer in length.**

---

Now we will use the _dnapars_ program (part of the _PHYLIP_ package) to search for the most parsimonious tree(s) for the ant dataset. The entire _PHYLIP_ package runs only on the command line, so you get to use Unix in Terminal again!!!

1. Create a folder on the Desktop named "Lab3" (using Terminal or on the Desktop using ctrl-click>"New Folder").
2. Put the concatenated sequence file in that folder.
3. Open a new shell/window in Terminal, and enter:
```bash
cd /Applications/phylip-3.695/exe/dnapars.app/Contents/MacOS/
```

4. Now you are working directly from the _PHYLIP_ application folder. Type "```ls```" and hit return, to see what's in this folder.
5. We want to perform a parsimony analysis using the function "```dnapars```", so enter the following command:
```bash
./dnapars
```
Now read the next line below!

The entire _PHYLIP_ package is highly user-unfriendly. The first thing you will see is that _dnapars_ "can't find the input file 'infile'". By default, the program looks for an input file by this name. Having not found that file, it asks the user to enter a new file name. So, do the following:

1. Click on the folder "Lab3" and drag it into the _Terminal_ window.
2. You will now see "Please enter a new file name>" followed by the path to the Lab3 folder. But we still need to specify the exact input file!
3. Add "```/[concat_sequences_file_name.phy]```" to the end of the string (after "```Lab3```", with no spaces). Of course use whatever actual file name you chose. It should look something like this:
```bash
Please enter a new file name> /Users/labuser/Desktop/Lab3/LWRh_wg.phy
```

4. Now hit return.

Argh, _dnapars_ complains again! Silly _dnapars_. It wants an output file name, too, like it wanted an input file.

1. Do the same steps 1-4 that you did above for the input file, **but** change the added file name to some new file name (for example, "```/Users/labuser/Desktop/LWRh_wg_out.phy```")

You may think that _dnapars_ is satisfied. Ha! _dnapars_ wants you to choose options to use in the search, and it's asking you several questions, with default answers listed. To change any of these answers, type the letter or number to the left of the question. **I recommend changing the input order of sequences to "randomize" and, after providing a seed number, tell it to jumble 20 times.** It will ask you for a random number seed, so enter an odd number. You should typically jumble more than this, but for the sake of time 20 is good. **Also make sure that the "I" option is set to the state that agrees with your file (sequential or interleaved).** This has to do with two different possible format variations in _PHYLIP_ format, but _Mesquite_ should automatically export in **sequential** format.

Finally, it is time to run _dnapars_! **Type "Y" to begin the analysis.**

Wow, _dnapars_ just can't let you rest. Now it wants an output tree file, so again specify a file name in the same manner as you did for the input and output files above, with a "```.tree```" extension (e.g. "```/LWRh_wg.tree```").

It's running! When the analysis finishes, **open the outfile (_not_ the tree file) in TextEdit**.


**Questions**
1. How many most-parsimonious trees were found?
2. What was the length of the most-parsimonius trees (i.e how many steps were required)?

**Now open the output tree file in TextEdit**. This has the most parsimonious trees in newick format with branch lengths. A little difficult to understand this way, right? In order to better understand the tree, you can open the tree file in the tree visualization program _FigTree_.

1. Open _FigTree_, and then open your tree file in the program.
2. It will ask what you would like to name the node/branch values, but just select "cancel".
3. You should now see the first tree, but there are multiple trees. Scroll through these trees using the arrows at the top of the screen.
4. Explore the different functions available in _FigTree_. For example, try rooting all the trees using the proper outgroups (we are interested in _Pseudomyrmex_, but included a few species of other genera as outgroups).

**Questions**
1. How do you highlight a particular clade?
1. How do you edit the name of a taxon?
3. How do you display branch length values?

## Bootstrapping in PHYLIP

We can use _bootstrap values_ to assess the robustness of a tree topology. To do this, we begin by creating a large number of _pseudoreplicate_ data sets from the initial dataset. Basically, this estimates how often the observed topology is recovered with alternative sampling of the dataset. Typically, researchers use at least 100-1000 pseudoreplicates, but to speed up the process today, we will just use 20. Once these data sets have been created, we then perform out tree search again on each data set, and again look for the most-parsimonious tree(s) (or the tree with the best _likelihood_, depending on the optimality criterion we are using). We then make a "majority-rule consensus tree" using the most-parsimonious tree(s) from each data set.

---
##### Bootstrap Scores
The proportion of times a clade is present in the pseudoreplicate trees is its bootstrap value. Typically, researchers take bootstrap values equal to or greater than 70 as evidence for good support for that clade.

---

We will run a bootstrap analysis in _PHYLIP_.

1. In Terminal, open the _seqboot_ program by first entering:
```bash
cd /Applications/phylip-3.695/exe/seqboot.app/Contents/MacOS/
```

2. Then enter "```./seqboot```".

3. Enter the original alignment filename as the input file, as you did earlier.

4. Type "R" and return to adjust the number of replicates (20).

5. Also remember to specify the format of your file (should be sequential) using "I".

6. Type "Y", hit return, and provide any odd number for the random number seed.

7. Specify the output file name as done earlier.

8. Open this new output file in _TextEdit_ and briefly examine it.

Now you get to find the most-parsimonious tree(s) for each of these 20 data sets:

1. Open _dnapars_ again, and specify your new file as the input file (the one with the 20 pseudoreplicate data sets).
2. Specify the output file.
3. Again, tell it to randomize the input order, but set the number to just 2 or 3 to save time.
4. Because we now have multiple data sets, type "M" and tell the program we are analyzing 20 data sets (not different weights).
5. Again, specify the format using "I" (this is getting old amirite?).
6. When finished, type "Y", hit enter, and, yet again, specify an output tree file.
7. This will take about five minutes to run. When it is finished, open the output file (NOT the tree file) in _TextEdit_.
8. **Question:** How many trees were saved for the first pseudoreplicated data set? How many for the second?

Now you have the privilege of constructing a majority-rule consensus tree to view the bootstrap support values for individual clades:

1. Open the _consense_ program in _PHYLIP_:
```bash
cd /Applications/phylip-3.695/exe/consense.app/Contents/MacOS/
```
Then enter "```./consense```".

2. Enter the input file name (the **tree file** from the bootstrap analysis), and designate an outfile.
3. When ready, type "Y", hit enter, and name the output files.
4. Open the output file in _TextEdit_ and look at the bootstrap scores. Also open the file in _FigTree_, and the bootstrap proportions wil be listed as "branch lengths" under the "node labels" option.

**Questions**
1. Notice that the bootstrap values never exceed 20. Why is that?
2. Are most of the clades supported (bootstrap value of 70% or greater - note that the values reported are not in percentage format!)?

---
##### Important Note
Notice that here, branch lengths have nothing to do with the amount of change in the consensus tree. The branch lengths indicate bootstrap support.

---

## Maximum Likelihood Using RAxML

**_RAxML_: Randomized Axelerated Maximum Likelihood** is a free program available for Mac, Windows, and Unix. RAxML also runs in terminal. However, unlike _PHYLIP_, no questions are asked, so we must use commands, which really are not that tricky. As the name implies, _RAxML_ uses the _Maximum Likelihood (ML)_ optimality criterion. It can also perform bootstrap analyses to assess topological support.

_RAxML_ begins by finding the most-parsimonious tree and uses this as a starting tree for the ML analysis. This saves time by starting with a fairly good tree and then searches for the tree according to a maximum likelihood framework. However, the real time-saving part has to do with clever statistics that can be used to speed up the analysis, but we won't discuss this here.

---
##### Models of Evolution
One of the points we discussed (or will discuss) in lecture is that a substitution model (aka model of evolution) must be specified. We will discuss model selection next week, but today, we won't do this because RAxML only allows one model (general time reversible or "GTR") with some slight variations. The most commonly used variants are GTR+GAMMA and GTR+GAMMA+INVARIANT, each of which includes additional parameters. The "gamma" parameter allows for sites to be divided into groups based on how fast they evolve. The "invariant" parameter specifies that some sites don't vary (evolve) at all, so we can specify the proportion of sites that do not vary.

---

1. Open a new Terminal window (shell) and type:
```bash
cd Desktop/Lab3/
```

2. Now you have to enter a string of commands to execute _RAxML_, and **they all need to be entered at the same time**. Items in brackets mean that you can/should change the item (e.g. use your actual file name, etc.). The string is:

```
raxml -f [a] -x [12345] -p [12345] -o [outgroup1,outgroup2] -# [number of bootstrap replicates] -m [GTRGAMMAI] -s [infile location and name] -n [outfile name] -T [4]
```

###### RAxML Parameters
- -f : Specifies the type of analysis you'd like to run. The "a" option searches for the best tree and does bootstrap replicates, generating a file with bootstrap values plotted on the best tree. You can stick with "a".
- -x : Specifies a random starting seed for the bootstrap replicates - just pick a random number!
- -p : Specifies a random starting seed for the maximum parsimony analysis. Again, just pick a random number!
- -o : Specifies any outgroups you have. List these with no spaces, with commas in between. We have three _Tetraponera_ species and one _Myrcidris_ species as outgroups. Use the actual names in your file!
- -# : Specifies the number of bootstrap replicates you want to do. You should do at least 100.
- -m : Specifies the model of evolution. For this dataset, I suggest either GTRGAMMA or GTRGAMMAI.
- -s : Specifies the name of the input file. You will need to specify the file location.
- -n : Specifies the name of the output files. Pick a name that will mean something to you!
- -T : Specifies number of threads. Should not be higher than the number of cores (processers/CPUs) on your machine.

**My command looks like this** (no line breaks in Terminal!):

```
raxml -f a -x 49181 -p 38333 -# 1000 -m GTRGAMMA -o T.pilosa,T.rufonigr,T.aethiops,M.epichari -s ./LWRh_wg.phy -n LWRh_wg_RAxML.tree -T 4
```

When you have formed the command with your own inputs, hit "enter". Hopefully it will begin running properly! It will take about 3 minutes, although this depends on the number of bootstrap replicates you use.

When the analysis ends:

1. Open the "bipartitions" file in _FigTree_ and tell it that the values it detects are "bootstrap".
2. Check the "node labels" box and tell it to display "bootstrap".
3. Look at the tree!

The mutualistic species are _P. concolor_, _P. tachigaliae_, _P. viduus_, _P. dendroicus_, _P. mixtecus_, _P. flavicornis_, _P. peperi_, _P. ferrugineus_, _P. veneficus_, _P. nigrocinctus_, _P. satanicus_, and _P. spinicola_.

**Questions**

1. Do you notice anything about the positions of the mutualistic species on the tree?
2. Compare the maximum likelihood and maximum parsimony trees. Are there big differences? Are there any differences? Which do you think is better, and why?

## Using RAxML at Home

## Email Me (bblanchard@uchicago)

- Lab file with questions filled in (please include your name(s) in the file name!)
- Consensus tree file with bootstrap values from _PHYLIP_ analysis
- Bipartitions file from RAxML

In this lab you ran RAxML through Terminal, which you can also do on your own computer at home. However, there is also a great resource that streamlines the process: the [CIPRES computing cluster](https://www.phylo.org/portal2/login!input.action). On _CIPRES_, you can upload files and run RAxML using external computing resources, which can save a ton of time and is WAY more convenient than having a memory-demanding process running in the background of your computer. When you use RAxML for your own analyses, you should probably just use _CIPRES_ - the lab today was to show you how RAxML works. After making an account, you can create a new "Task" and under "Use Tool" choose one of the "RAxML" options.

The basic input is the same as the input you used today - a list of sequences in PHYLIP format, which you produced at the beginning of the lab. But there are several other parameters that you can add to the analysis in _CIPRES_ (and in _PHYLIP_, but we just didn't use them today!). One important feature is the **partitioned model** option. This allows you to use a different model of evolution for each gene in your dataset, and in _CIPRES_ is set by uploading a file under the "Use a mixed/partitioned model?" option.

---
##### RAxML Data Partitioning File Format
In order to designate where genes are located along your entire concatenated matrix, RAxML requires a plain text file in a particular format. Each line of the file designates one partition. For example, if you wanted to partition the two genes we used to today, the file would read:

DNA, LWRh = 1-481

DNA, Wg = 482-884

---
For each line, the first item indicates that the data is DNA, then the second item gives a name to each partition and tells the program the location of this partition.

There are a few other options in RAxML, and you can read more about each parameter when you make your Task on _CIPRES_ - you can click the name of each parameter to get more information about it.
