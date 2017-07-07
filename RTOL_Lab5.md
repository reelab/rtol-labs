
# Lab 5: Ancestral State Reconstruction & Independent Contrasts

<img src="http://www.alexanderwild.com/Ants/Natural-History/Ants-and-Plants/i-bzZ6sTP/2/XL/spinicola16-XL.jpg" style="float: left; width: 49%; margin-left: 1%; margin-top: 1.39%; margin-bottom: 4%"><img src="http://www.alexanderwild.com/Ants/Natural-History/Ants-and-Plants/i-NX9Gwb6/1/XL/Belize1-XL.jpg" style="float: left; width: 49%; margin-right: 1%; margin-bottom: 4%"><p style="clear: both;">

Phylogenetic trees are most interesting when they're used to infer processes of evolution beyond just how species are related to one another. Today, you will reconstruct ancestral states using parsimony and maximum likelihood methods to infer processes of trait evolution (specifically mutualism) in _Pseudomyrmex_. By the end of today, you will be able to:

1. Reconstruct ancestral states using parsimony and likelihood.
2. Use independent contrasts to determine whether two characters are correlated.

## R: Time Calibration

In order to do the ancestral state reconstruction, you want the tree to be **ultrametric**, which means that there are equal root-to-tip path lengths for all lineages in your tree. In other words, if your tree is ultrametric, it means that all of the tips in your tree line up evenly.

---
**Question:**
- Why do you think you need an ultrametric tree to do ancestral state reconstruction?

---
**Open the maximum likelihood tree that you generated in _RAxML_ a couple weeks ago in _FigTree_** (on the course website). Notice how the terminal taxa are not lined up? In order to make your tree ultrametric, you have to shrink some branches and lengthen others based on some optimal rate of evolution that fits a model to the branch lengths that you calculated based on molecular substitution. Today, you will do this using the program _R_, which you may remember using way back in the first lab! Those were the days...

Many biologists implement and share their work in _R_, meaning that there is a lot of pre-made software available for doing cool bio stuff. You can compile large amounts of code into _R_ scripts or work in the _R_ GUI going one command at a time like you do in _Terminal_, which you will do today.

1. Open the _RStudio_ application (should be one of the big "R" applications at the bottom of your screen).
2. First, you have to load the "APE" (Analysis of Phylogenetics and Evolution). You first have to download the package from a data repository called "CRAN", then load the package locally:
```r
install.packages("ape")
library(ape)
```
3. Now you have to change your working directory to the folder that has your tree file (downloaded from the course website). Remember, you can use the `setwd()` command, and unlike in _Terminal_ you have to use quotes ("") around your file path!

4. Now loading the tree is simple, using the APE function `read.tree`:
```r
ant_tree <- read.tree("LWRh_wg.tree")
```

5. Enter "`ant_tree`" to get a summary of the information contained in the tree.

6. Now enter:
```r
plot(ant_tree)
```

Ooh, fancy! _R_ can be great for making very nice figures as is obvious from your ability to quickly display this tree. In R studio, you can also click "Zoom" in the plot pane to see a larger version that may be easier to read. You can also adjust a bunch of things about plots in R, which you will do a bit later.


Now it's time to make the tree ultrametric, using `chronos`:

1. Enter:
```r
ant_tree_ultra <- chronos(ant_tree)
```
2. Now plot the tree again!

You should see that now all the tips line up (i.e. the tree is ultrametric!). `chronos` does this by using certain parameters and an algorithm to convert a tree with nucleotide substitution information into an ultrametric tree. You don't need to know about these parameters right now, but just know that it is possible for the user to adjust them! 

## R: Ancestral State Reconstruction - Parsimony


<img src="http://2.bp.blogspot.com/-jt_Jc13EZqc/UUoqO_hjbrI/AAAAAAAACC0/gMp44Wg2ZDI/s1600/lik.anc2.png" style="float: left; width: 40%; margin-left: 5%"><img src="http://www.alexanderwild.com/Ants/Making-a-Living/Arboreal-Ants/i-5Fhfx4P/2/XL/spinicola10-XL.jpg" style="float: left; width: 25%; margin-right: 1%"><p style="clear: both;">

Now that you have your gorgeous ultrametric tree, it's time to start mapping traits on the tree and inferring things about trait evolution! But you also need to import the character information for the terminal taxa for reconstructing ancestral states.  **Open the trait file in _TextEdit_**. This is a simple matrix of characters for a number of species in the genus. Most of them are morphological measurements, but the first two columns indicate whether the species is symbiotic with plants (0=no, 1=nests mutualistically, 2=nests parasitically). You want to reconstruct ancestral states for mutualism so that you can understand how this behavior has evolved within _Pseudomyrmex_.

1. Make sure the text file of traits from the course website ("pseudomyrmex_characters_pruned.txt") in your working directory (if it's not, you can move it to the directory manually).
2. For ease of typing, change the name of your tree back to `ant_tree`:
```r
ant_tree <- ant_tree_ultra
```
3. To load the table of characters in _R_, enter:
```r
traits <- read.table("pseudomyrmex_characters_pruned.txt",header=TRUE)
```
The "header=TRUE" item tells _R_ that the first row of the file contains the names of the columns and is not data itself.
4. Enter "`traits`" to see the table within _R_.
5. You want to put mutualism character information into a form easily transferable to the tree and with the correct names corresponding to taxa in the tree, so enter:
```r
mut_data <- traits$Mutualists
names(mut_data) <- row.names(traits)
```
6. Check to make sure everything looks good by entering `mut_data`. You should see all the taxa with a corresponding 0 or 1 indicating mutualism.
7. There are several polytomies in your tree, which unfortunately the APE package doesn't like. So, to "prune" (get rid of) some taxa, and also drop all but one of the outgroups, execute this command (feel free to copy-paste):
```r
new_ant_tree <- drop.tip(ant_tree, c("P.mixtecus","P.flavicor","P.peperi","P.spinicol","P.kuenckel","P.veneficu","T.pilosa","T.rufonigr","T.aethiops"))
```
8. Now display this new tree with reduced taxa:
```r
plot(new_ant_tree)
```
Notice the absence of polytomies!
9. To make your tree look _really_ purty, enter the following (copy-paste):

```bash
new_ant_tree[6] <- "tip.state"
new_ant_tree$tip.state <- traits$Mutualists
names(new_ant_tree$tip.state) <- rownames(traits)
new_ant_tree$tip.state <- new_ant_tree$tip.state[new_ant_tree$tip.label]
state1 <- row(matrix(new_ant_tree$tip.label))[new_ant_tree$tip.state == 1]
state0 <- row(matrix(new_ant_tree$tip.label))[new_ant_tree$tip.state == 0]
plot(new_ant_tree, label.offset = 0.02)
tiplabels(tip = state1, pch = 21, cex = .9, bg = 'red')
tiplabels(tip = state0, pch = 21, cex = .9, bg = 'yellow')
```

This looks complicated, and perhaps is a bit, but it is a great way to label the tips of your tree with states and permanently associate the tip states with specific taxa.

---
**Question**: What do the red dots represent? What do the yellow dots represent?

---
Now time for the ancestral trait reconstruction! You will start with a most parsimonious reconstruction:

1. The APE package only accepts unrooted trees for parsimony reconstructions, so enter:
```
unrooted_tree = unroot(new_ant_tree)
```
2. Now run the ancestral state reconstruction:
```
pars_anc <- MPR(unrooted_tree$tip.state,unrooted_tree,"M.epichari")
```
You will get a warning - that is ok!
3. "`pars_anc`" will include the reconstructed states at each node. Before displaying this, you have to trim the remaining outgroup taxon, and display the tree with node labels:
```r
new_ant_tree <- drop.tip(unrooted_tree,"M.epichari")
plot(new_ant_tree, label.offset = 0.02)
tiplabels(tip = state1, pch = 21, cex = 0.9, bg = 'red')
tiplabels(tip = state0, pch = 21, cex = 0.9, bg = 'yellow')
nodelabels(pie = pars_anc[,1],piecol = c('red','yellow'),cex=0.5)
```


Voila! The node labels are pie charts that indicate the probability of each state at that node. Because this is a parsimony reconstruction, the nodes are 100% one state or the other. The pie charts become more relevant for the likelihood reconstruction.

---
**QUESTIONS:**
- According to this parsimony ancestral state reconstruction, how many times did mutualism **independently** evolve?
- Are there any inferred losses of mutualism?

## R: Ancestral State Reconstruction - Likelihood

With the tree already reformatted and all, running the likelihood reconstruction is now rather straightforward. First, you have to do a quick reassignment of the data to the tips, so enter:

```bash
new_ant_tree$tip.state <- new_ant_tree$tip.state[1:33]
```

Now you will reconstruct ancestral states using the "`ace`" function in APE using two basic models for reconstructing ancestral states. First, you specify the one-rate "Mk1" model with the "`matrix(c(0,1,1,0),2)`", which indicates that transition rates are equal (i.e. the rate from state 0 to state 1 is the same as the rate from state 1 to state 0). Similarly, you can specify a two-rate model with "`matrix(c(0,1,2,0),2)`", where these two transition rates are _not_ constrained to be equal.

1. For the one-rate model, enter the following:
```r
anc_mk1 <- ace(new_ant_tree$tip.state,new_ant_tree,type='discrete',model=matrix(c(0,1,1,0),2))
```

2. Then display the results: 
```r
nodelabels(pie=anc_mk1$lik.anc,piecol=c("yellow","red"),cex=0.5)
```
3. Now try the two-rate model! Enter:

```r
anc_mk2 <- ace(new_ant_tree$tip.state,new_ant_tree,type='discrete',model=matrix(c(0,1,2,0),2))

nodelabels(pie=anc_mk2$lik.anc, piecol=c("yellow", "red"), cex=0.5)
```

---
**Questions:**
- Do the results for the two different rate models differ? If so, describe the difference.
- Why do you think the results could differ between the one-rate and two-rate models? Which rate model do you think is a closer match to what actually happens in reality? Why?

## R: Independent Contrasts

You can also use _R_ to analyze phylogenetically independent contrasts (PIC), which means removing the effects of phylogenetic relatedness when determining if traits are correlated. Traits can seem "correlated" when in fact they are merely associated among taxa that are all closely related, due to common ancestry. So you want to account for this relatedness using PIC.

1. Look at the "pseudomyrmex_characters_pruned.txt" file in _TextEdit_ to see the names of a number of morphological traits (e.g. N3, N4, REL, HW, PL).
2. You can plot characters against each other, for e.g. head length (HL) and head width (HW), using:

```bash
plot(traits$HL,traits$HW)
```
3. You can see a very strong correlation between these traits (unsurprisingly). But you want to control for phylogeny using PIC:

```bash
HL <- traits$HL
names(HL) <- row.names(traits)
contrastHL <- pic(HL,new_ant_tree)
HW = traits$HW
names(HW) <- row.names(traits)
contrastHW <- pic(HW,new_ant_tree)

plot(contrastHL,contrastHW)
```

**Questions:**
- Do you still see a strong correlation using PIC?
- If so, what does this indicate?

## Mesquite: Ancestral State Reconstruction - Parsimony
<img src="http://mesquiteproject.org/mesquite0.992/Mesquite_Folder/docs/mesquite/AncestralStates/images/anceStates.jpg" style="float: left; width: 45%; margin-left: 1%"><img src="http://www.alexanderwild.com/Ants/Taxonomic-List-of-Ant-Genera/Pseudomyrmex/i-xSGJtMh/1/XL/peperi8-XL.jpg" style="float: left; width: 48%; margin-right: 1%"><p style="clear: both;">

You can also use _Mesquite_ to run ancestral state reconstructions, and this can sometimes be an easier way to visualize the results.

1. Open _Mesquite_ and load the file "LWRh_wg_ultra_chars.nex" (from the course website)
2. Click "Imported trees>View Trees" on the left-hand side.
3. Now in the tree window, to get rid of the support values so the tree looks nicer: Display>Names>Show Branch Names
4. Show branch lengths on the tree: Display>Branch Proportional to Lengths
5. Now, to show ancestral states using parsimony: Analysis>Trace Character History
6. Choose "Parsimony Ancestral States", then "Mutualism".
7. **Question:** How many steps (changes in character states) are necessary in the most parsimonious ancestral state reconstruction?

You have weighted changes in behavior equally. However, you can edit the step matrix to penalize some changes more than others. For instance, you may think that changes in one direction might be quite easy, but a change in the reverse direction may be extremely unlikely. To edit this:

1. Go to Characters>New Character Model>Step (Cost) Matrix
2. Create a name for this new step matrix.
3. A massive step matrix will appear. To prune this down to the number of character steps actually present, go to: Step Matrix>Set Maximum State.
4. You have two character states, and the "maximum state" refers to the highest state number you have assigned, in this case "1" (you have states 0 and 1).
5. Change the step matrix values by clicking on the "edit" tool to the left of the step matrix (below the arrow), and then edit the matrix. See the cells with a "1"? These mean that the changes from state 0 to 1, and from state 1 to 0, both "cost" 1 step. Change these values so that one rate is more costly than the other, and see how this changes the parsimony of the tree.
6. After making changes, go back to the tree, and go to: Trace>Parsimony model.
7. Choose your new model.

**Questions:**
- Has the ancestral state reconstruction changed?
- If so, how, and how many steps are now required?

## Mesquite: Ancestral State Reconstruction - Likelihood



Like with the APE package, you have to prune the 0-length branches (polytomies) before performing likelihood reconstructions in _Mesquite_.

1. The taxa causing the problem are in the largest mutualistic group. Click the scissor tool for pruning taxa and remove _P. peperi_, _P.flavicor_, and _P. mixtecus_. You need to click on the branch leading to each individual taxon.
2. Now go to: Trace>Reconstruction Method>Likelihood Ancestral States>Stored Probability Model>Mk1 (this is the symmetric 1-parameter model)
3. Change the tree form: Display>Tree Form>Balls and Sticks. Now pie charts are over each node showing the proportional likelihood for each character state at that node.
4. **Question:** You should see a small "Trace Character" window. What is the -log likelihood value for the fit of this data to the model?
5. Now change the model to the asymmetric 2-parameter model: Analysis>Trace Character History>Likelihood Ancestral States>Stored Probability Model>Mutualism>Asymm. 2 param.
6. **Question:** A second "Trace Character" window has appeared for the "Asymm. 2 param." model. What is the -log likelihood value?

But which model is best? Like last week, you can do model testing, but instead of using the AIC, you can use the Likelihood Ratio Test (LRT):
1. Note the likelihood under the Mk1 model and the likelihood under the asymmetric 2-parameter model.
2. Multiply the difference between these two likelihoods by 2.
3. Take the absolute value of this and look up this value in a table of critical values (with 1 degree of freedom, because the two models differ by one parameter). See [here](https://malouffapbioblog.files.wordpress.com/2013/05/table-1.jpg) for a table. If it is **larger** than the value listed under "0.05" (3.84), then the difference between the two models is **significant**. If it is **smaller**, then there is no significant difference in likelihood between the models.

---
**Questions:**
- What value did you get for the LRT (difference between the two likelihoods)?
- Which model is favored? Remember, one model has two parameters, and the other has one.

## Email Me (bblanchard@uchicago.edu)
- Completed lab file (in .ipynb format, w/your name(s) in the file name)
- And that's it for this week!
