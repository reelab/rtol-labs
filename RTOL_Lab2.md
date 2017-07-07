
# Lab 2: Homology and Sequence Alignment

<img src="https://storylinepr.files.wordpress.com/2012/09/apple-and-orange-banner.jpg" width="500" height="10">
<img src="https://storylinepr.files.wordpress.com/2012/09/apple-and-orange-banner.jpg" width="500" height="10">


## Introduction

Last week, you learned how to download sequences from GenBank. But how are you supposed analyze these sequences in a reasonable way? This week, you will learn how to **align** your sequences so you can later use them to infer processes about the evolutionary history of life.

The goal of **alignment** is to line up **homologous** nucleotide or amino acid positions across individuals/species. Ensuring homology of sequence regions that we are comparing is very important, because otherwise we would be treating two different regions as the same, leading to potentially unreasonable conclusions. For example, if I wanted to infer relatedness between everyone in this class, I could choose one trait - say the right thumb. I would want to make sure that I always select the right thumb for each student in the class, and not the left thumb, or the eye, because the ways in which other traits differ may not be comparable to the variation in the right thumb. In other words - we don't want to compare apples and oranges (although both are delicious).

While aligning sequences may sounds simple, it can actually be rather tricky. For instance, some loci (such as parts of the ribosomal DNA) tend to have many insertions and deletions across homologous regions between individuals and species, and the placement of homologous bases can become rather ambiguous.

## Importing and Manually Aligning Sequences

For the assigment last week, you should have used _GenBank_ to create FASTA-formatted sequences files of the wingless (wg) and long-wavelength rhodopsin (LR) genes for 43 species of ants. **Open "wg.fasta" in a text editing program (e.g. TextEdit).** How does it look? Probably a little unorganized, particularly with the long and confusing descriptions for each sequence name. In order to clean it up a little bit, we're going to use Unix in Terminal (like we did last week!).

1. Open the "Terminal" application
2. Make a directory. Move "wg.fasta" and "LWRh.fasta" to that directory.
3. Change the working directory to this new directory.
4. Type the following:
```bash
cat wg.fasta | sed 's/>.*\.1| //' | sed 's/\(^[A-Z]\)[a-z]* />\1\./' | sed 's/ .*[a-z]//' > wg_clean.fasta
```
5. Excute the same command on "LWRh.fasta" instead of "wg.fasta" (change "wg_clean.fasta" to "LWRh_clean.fasta", too!)

If you want to understand what this command is doing, ask me! Essentially, the command contains a number of filters composed of "regular expressions" to change the format of the file. Regular expressions can be a little confusing to interpret at first - see [here](http://www.tutorialspoint.com/unix/unix-regular-expressions.htm) for some information on regular expression using the unix command `sed`.

**Take a look at the resulting files**. Don't they look nicer? Generally, we would probably want to keep the _GenBank_ accession numbers with the sequences that we are using so that we remember exactly where they came from. But for this lab, we just need the species names!

Now, let's do some aligning!

### Manual Alignment in Mesquite 

<img src="http://www.alexanderwild.com/Ants/Taxonomic-List-of-Ant-Genera/Pseudomyrmex/i-ZHW3q3f/1/XL/termitarius1-XL.jpg" style="float: left; width: 32%; margin-right: 1%; margin-bottom: 0.5em;"><img src="http://www.alexanderwild.com/Ants/Taxonomic-List-of-Ant-Genera/Tetraponera/i-xFN6H3M/1/XL/mocquerysi2-XL.jpg" style="float: left; width: 30%; margin-right: 1%; margin-bottom: 0.5em;"><img src="http://www.alexanderwild.com/Ants/Taxonomic-List-of-Ant-Genera/Pseudomyrmex/i-8HRhKCf/1/XL/gracilis7-XL.jpg" style="float: left; width: 30%; margin-right: 1%; margin-bottom: 0.5em;"><p style="clear: both;">

Mesquite (project site [here](http://mesquiteproject.wikispaces.com)) is a free program primarily geared towards evolutionary analyses. A very wide range of phylogenetic analyses can be performed with it - such as sequence alignment, ancestral state reconstruction, and character evolution estimation - and we will be using it again later in the course. Sometimes it can be a little clunky and slow, but it's still worthwhile to use! Take some time to explore the project website and see what Mesquite offers. In this lab, we will use it to import, visualize, and manually align DNA sequences.

1. Open Mesquite (This can take a bit. Patience comes to those who wait). Make sure you choose the program that's just called "Mesquite".
2. Once it fully loads, go to File -> Open File -> specify your file ("wg_clean.fasta").
3. A separate window will appear asking for the file format - select the correct format, and click "OK".
4. Now it will ask you to specify a name you'd like for this file (I've heard "wg_clean.nex" is popular these days). Click "Save".
5. We are now ready to view the sequences - if the matrix doesn't show automatically, click on "Character Matrix" and select "Show Matrix".

Colors! Each nucleotide gets its own color, for ease of viewing. Scroll around to look at the sequences - because these ants are all pretty closely related, their sequences are generally lined up pretty well, but not perfectly, as you can see. 

- **Question**: Why are the gene sequences not always perfectly aligned? Homologous genes should all be the same length, right?

Mesquite has several tools that you can use to help manually align sequences. Explore the tools in the pane just left of the sequence names.

***ADD SCREENSHOTS OF TOOLS?***

- _Add Characters_: You can add a gap character between any two characters or at the end of the sequences.

- _Push Sequence_: Used to push sequences left or right. This can only be used if there are gaps in the sequence. 

- _Multiple Sequence Splitter_: You can draw a verticle line between character positions across multiple sequences and then move the split sequences in blocks.

- _Move Blocks_: Used to move parts of a single sequence either by pushing the whole sequence or splitting between two bases.

- _Multiple Sequence Move Blocks_: You can highlight multiple sequences and then move them as with the single sequence "Move Blocks" tool.

Remember, the only way to move a sequence around is into gaps, so if you need to move the whole sequence to the right there needs to be gaps at the end.

Familiarize yourself with these tools, and then **manually align the sequences to one another**. There are a few places that need alignment.

**Questions:**
1. Are conserved areas and variable sites evenly distributed? If not, what variation do you see?

2. Did you encounter any areas where it was difficult to determine the position of a certain base or group of bases? If you did, do you think these should be included in the phylogenetic analyses? Why or why not?

3. What kind of feeling does manual alignment give you?

### Automated Alignment - Clustal and MUSCLE

<img src="http://icons.iconarchive.com/icons/iconshow/medical/256/Muscles-icon.png" style="float: left; width: 20%; margin-left: 25%; margin-bottom: 0.5em;"><img src="http://www.clustal.org/images/clustalo_big.png" style="float: left; width: 20%; margin-right: 30%; margin-bottom: 0.5em;"><p style="clear: both;">
If your data set is small or contains little variation across taxa, there may really be no need for automated alignment. In most cases, however, datasets are so large and/or variable that it's best to produce an automated starting alignment that you can then manually refine. Today, you will get a feel for two of the most popular automated alignment programs: Clustal and MUSCLE.

---
#### Clustal
Clustal is used for multiple sequence alignment, and comes in two flavors. ClustalX comes with a GUI (Graphical User Interface), meaning it has menus and is easy to visualize. ClustalW does _not_ have a GUI, and commands must be entered directly in the command line (like you did earlier in Terminal). The alignment methodology for both programs is the same, but today we will just work with ClustalX.

---

1. Go to "www.clustal.org/download/current", and download "clustalx-2.1-macosx.dmg.
2. Once it is in the Downloads folder, double click on it, then open "ClustalX".
3. Load your unaligned sequences by going to "File>Load Sequences" and selecting the file. You should now see a screen that resembles Mesquite (colors!).
4. **Question:** Along the top, you should see asterisks at some sites. What does an astrisk above a site indicate?

Along the bottom, you will notice what looks like a histogram (bar chart) - these are the "alignment quality scores" for each character/position. Loosely speaking, the higher the alignement quality score, the more "conserved" this site is, meaning the less variability there is at the site across the included taxa.

Now it's time to perform a multiple alignment. At the top, next to the "Mode" tab, there is the option of performing a "multiple" or "profile" alignment, the latter of which can be used when you already have an alignment and want to add sequences. But we want "multiple alignment".

This process proceeds in three steps:

1. Pairwise alignment: Creates matrix of "distance scores" documenting the amount of difference between each possible pair of sequences.
2. Reconstruct simple tree based on distance scores.
3. Uses simple tree as a guide tree to calculate the alignment: Begins at tips with greatest similarity and aligns these, then proceeds "up" the tree, aligning subsequent sequences.

So, how does the program actually determine alignments as it goes along? Yep, you guessed it! By trying to minimize the number of mismatches and gaps, thereby creating an "optimal" alignment. The program gives a certain penalty score to both a mismatch ("mismatch penalty") and a gap ("gap penalty"), and uses these penalties in its calculations.

Exciting, right??? Well, now it's your turn!

1. Go to "Alignment>Do Complete Alignment"
2. Provide a name for the output file for the guide tree, and the name for the final alignment
3. Once you are ready, hit "OK", and take a look at the alignment!
4. **Question:** How does the alignment look? Do you see any noticeable errors?

As you can see (hopefully), automated alignment can save a lot of time (imagine if you had to manually align 1,000 sequences!). However, sometimes some areas are incorrectly aligned, and so researchers will often "manually refine" their alignments.

**Export the aligned sequences, in FASTA format - but give the output file a DIFFERENT NAME (otherwise it will overwrite a file).**

**Now repeat for the other gene (but don't answer the questions again, just do the alignment!).**

---
##### Note: Changing Alignment Parameters
Several parameters can be adjusted by going to "Alignment>Alignment Parameters", and then you choose what you'd like to adjust. For instance, you could choose "Multiple Alignment Parameters" and you will see a variety of parameters you can change.

---

#### MUSCLE (MUltiple Sequence Comparison by Log-Expectation)

It may seem redundant to examine another program, but it's important to know that many programs calculate alignments differently and perform well in different situations. MUSCLE is a newer program that performs better than Clustal in some situations, but one downside is that it doesn't come with a nice GUI. We won't discuss how MUSCLE differs from Clustal, but you can see the original paper ([Edgar 2004](http://www.ncbi.nlm.nih.gov/pubmed/15034147)) if you are interested.

1. Go to www.drive5.com/muscle/downloads.htm.
2. Select "muscle3.8.31_i86darwin64.tar.gz"
3. This file we be placed in your downloads - move it to the directory that you made earlier
4. Double-click on the file and it will generate an executable.
5. Rename this executable file to "muscle". One way you could do this is in Terminal:
```bash
mv muscle3.8.31_i86darwin64 muscle
#We used the `mv` command last week to move a file into a directory, but you can also use it to rename files if the second item is not a directory!
```

6. Open Terminal, if it isn't open already. Check to make sure you are in the correct working directory, and change the directory if you aren't.
7. We don't have Administrator access, so we have to specify the MUSCLE file using "`./`". So, enter this command:
```bash
./muscle
```
This provides a list of paramaters that you can use to run MUSCLE. All you _need_ is an input FASTA file with sequences, and an output file name. So type:
```bash
./muscle -in wg_clean.fasta -out wg_clean.afa
```
The file with aligned sequences will now be placed in your working directory (the ".afa" stands for "aligned FASTA").

8. Repeat for the LWRh gene.

**Open both the exported Clustal file (from above) and the MUSCLE-aligned file in Mesquite**.

- **Question**: How do the two aligned datasets for wingless (wg) compare? Do you see any differences? If so, where?

##### Practice
Now try using MUSCLE to **align the sequences in "Malia_et_al_2002.fasta"** (available on the [course website](https://sites.google.com/a/fieldmuseum.org/rtol/lab-exercises)). These sequences are from a growth hormone receptor gene in a diversity of mammals.

Once you have exported the alignment (.afa file), look at the alignment in Mesquite.

- **Question:** How does the clarity of this alignment compare to the aligned dataset for the wingless (wg) gene in ants? Why do you think this is?

### Aligning Protein-Coding Genes

Look again at the aligned ant sequences for Wingless (wg) from the MUSCLE alignment, and try to make them even better with manual alignment. How do you feel about the alignment? Are there any sites that could be aligned differently? For example, why is the cytosine at position 97 in _P. concolor_ not instead at position 94? How arbitrary!!! Wouldn't it be nice if we had addtional information to resolve these situations? Oh wait..... we do!

Wingless (wg) and Long_Wave Rhodopsis (LWRh) are both protein-coding genes, and it can be much easier to align sequences by amino acids rather than nucleotides. (Do you know why this is true? Think about what level selection acts on when it comes to proteins.) We will do this in Mesquite.

1. Load the aligned sequences for wingless (wg) into Mesquite.
2. Go to the "Characters" menu in Mesquite.
3. Select "List of Characters".
4. Click on the arrow above where it says "Character", and select "Select All Characters".
5. Go over to the "Codon Position" column and click the little arrow at the top.
6. Go to "Set Codon Position" and chose "123123..." to desginate the first base as codon position 1 (which may not be correct, but we will see in a moment).
7. Go back to the character matrix, and go to "Characters>Make New Matrix from>Translate DNA to Protein", and take a look at this matrix of codons. Black indicates a stop codon.
8. **Question:** Do you think this is the right codon positioning? Why or why not?
9. Try it with "231231..." and "312312...".
10. **Question:** What codon position is the first base in the sequence?
11. Now manually align these DNA sequences using the amino acids, if needed.
12. **Question:** Was this alignment easier?


**Now check out the aligned Malia et al. (2002) sequences and try to figure out what the codon positions are.**

- **Question:** At which site does the gene start? Remember that methionines (M) act as start codons, and that some of these sequences are only _partial_ cds).

---
##### Hyopthetical

Imagine you generated a sequence for a protein-coding gene by PCRing genomic DNA. You are trying to align it to some other sequences and the amino acids align very well. But all of a sudden, your new sequence deviates sharply from the other sequences and you cannot find any homology. Additionally, you see a stop codon in this area that differs greatly from the rest of the sequences.

- **Question**: What do you think has happened here?

---

For more information on aligning nucleotides by amino acids in Mesquite, see [here](http://mesquiteproject.wikispaces.com/Managing+Molecular+Data#managing).

We will use the aligned sequences to infer trees next week, so make sure you have good alignments for both ant genes!

### Email Me (bblanchard@uchicago.edu)

1. Lab file with answered questions
2. Exported alignment file for **both genes** from Clustal alignment
3. Output alignment file for **both genes** from MUSCLE alignment
4. Alignment file from the Malia et al. 2002 data


