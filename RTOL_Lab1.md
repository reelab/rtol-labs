# Lab 1: Introduction to Handling Phylogenetic Data

![big tree](http://bigtreestrategies.com/wp-content/uploads/2011/07/BigTree.jpg) 

## Objectives

By the end of the lab, you should be able to do the following:

- Basic navigation in R and Unix
- Search/access information from NCBI databases and examine accessions
- Use search limits
- Perform BLAST searches
- Download sequences
- Search and access information from TreeBase

## Introduction

DNA sequences are the most common type of data used to reconstruct phylogeny. While some use phenotypic characters (primarily paleontologists) to construct phylogenies and explore evolutionary trends, most studying extant organisms use DNA sequence data. We will learn more about how to analyze these data in upcoming labs, but here we begin by learning some basic commands in useful languages, and how to search for and access publicly available DNA sequence data (and some morphological data).

## Section 1: Commonly Used Commands in Unix and R

### Unix (Terminal)

We will be using Unix for some of the labs in this class, so it’s important to understand the basics in order to feel comfortable navigating phylogenetic programs that require it. Generally, commands in Unix work by typing the command, hitting space bar, and then entering the necessary files or information for that command, also separated by spaces. 

|Command|Function|
|:---------:|:----------:|
|`pwd`|Print working directory (tells you where you are)|
|`cd`|Set working directory|
|`mkdir`|Make directory|
|`ls`|List information about files in working directory|
|`mv`|Moves or renames files or directories|
|`rm`|Deletes files or directories|
|`man`|Access manual for any command (exit = `q`)|
|`head`|Look at first ten lines of file (useful for large files)|
|`tail`|Look at last ten lines of file (useful for large files)|
|`cat`|Concatenate and print content of files|
|`curl`|Transfer a URL (downloads something from the internet)|
|`tar`|Creates tape archives and adds or extracts files|
|`ssh`|Secure Shell client (remote log-in command)|
|`emacs`|Opens emacs text editor|
|`top`|Observe computer’s tasks|
|`./`|Existing directory (not a command)|
|`../`|Directory containing existing directory (not a command)|

### Practice
1. Open a Terminal (a new one, not the one running this notebook).
2. Look at what directory you are in.
```bash
pwd
```
3. Make a new directory (name it whatever you want).
```bash
mkdir your_directory/
```
4. Make a second directory.
5. Move one of your directories into the other.
```bash
mv your_directory/ dir2/
```
6. Change working directory to your new directory. 
```bash
cd dir2/
```
7. Look inside your new directory.
```bash
ls
```
For a more detailed look:
```bash
ls –l
```
8. Move back to the original directory.
```bash
cd ..
```
9. Read more about the command `rm`
```bash
man rm
```
Use `<space>` to page through the documentation, and `q` to quit.
10. Delete both directories you’ve made.
```bash
rm –r dir2/
```

##### Warning!
* **`rm -r`** will delete the file or directory you list and *everything* inside of it. It will not ask you whether you are sure. BE CAREFUL using it. 

Read more about Unix and practice using it, as it is a useful tool that may help you later.

---
### Statistical Computing in R

Like Unix, we will be using R for labs in this class, so it’s also important to get comfortable with it. Although R can be run from within Unix, we will run it in the R console. R provides a wide variety of statistical and graphical techniques, and is widely used by biologists. 

|Command|Function|
|:---------:|:----------:|
|`getwd()`|Print working directory (equivalent to “pwd” in Unix)|
|`setwd("[dir/]")`|Set working directory (equivalent to “cd” in Unix)|
|`c(a,b,c)`|Concatenate *numbers* a,b,c into *vector*|
|`c(a:z)`|Concatenate *numbers* from a to z into a *vector*|
|`cbind(x,y,z)`|Concatenate *vectors* x,y,z into *matrix* by columns|
|`rbind(x,y,z)`|Concatenate *vectors* x,y,z into *matrix* by rows|
|`class(x)`|Return class of objext x|
|`matrix(x,nrow,ncol)`|Builds *matrix* by dividing *vector* x into nrow and ncol|
|`plot(x,y)`|Plots elements of *vector* x against *vector* y|
|`density(x)`|Estimates density distribution of univeriate observations|
|`read.table("file.txt")`|Read text file that is in a table format|
|`rnorm(n)`|Generates a normally distributed *vector* of length n|
|`?[function]`|Access documentation for a given function (e.g. ?rnorm)|

In R, it is very easy to quickly define variables and store data of interest as variables. This is one of the reasons it is used by a wide variety of disciplines. Variables are usually defined using a combination of functions (see basic functions listed above) and data, using the "<-" or "=" symbols.  One way of obtaining data is to generate it within R. We will do this first:

### Practice
1. Open the R console.
2. Build and name a 10x10 matrix with integers from a normal distribution.
```R
A <- matrix(rnorm(100),10,10)
```

3. Look at the matrix you built (A).

4. Plot the first and second columns against each other.
```R
plot(A[,1],A[,2])
```

5. Name your plot title and axes.
```R
plot(A[,1],A[,2],main="Title",xlab="X axis",ylab="Y axis")
```

6. Change your matrix to a dataframe.
```R
A <- as.data.frame(A)
```

7. Check the class of your dataframe (using "class").

8. Plot the first and second columns from the dataframe.
```bash
plot(A$V1,A$V2)
```

Another way of obtaining data is to import it from outside R:

9. Import data into B (Note: You have to download "test.txt" from the course site, and have it in correct directory!).
```r
b <- read.table("test.txt",header = TRUE)
```

10. Check class of object.
```r
class(B)
```

11. Plot "Lineages" against "Age".
```bash
plot(B$Age,B$Lineages)
```

R can do lots of other useful things, so spend some time outside of class getting used to it!

#### Tab Completion
In both Unix and R, you can hit tab to finish your command or function while you are typing to save you time. This is particularly helpful with long file names.

## Secton 2: Searching for and Obtaining Data from Online Databases

<img src="http://buzzsharer.com/wp-content/uploads/2015/06/beautiful-running-horse.jpg" width="700" height="10">

_GenBank_ (http://www.ncbi.nlm.nih.gov/Genbank/index.html) is a free, publicly available online database for DNA sequence data. It contains loads of data.  Nearly all published studies are required (by the journal) to deposit any new sequence data in _GenBank_. It is part of the _NCBI_ cluster (http://www.ncbi.nlm.nih.gov/), which includes several databases (literature, molecular, and genome), as well as tools for searching and analyzing the databases, tutorials and educational links, and information on submitting your own data. **Take a few minutes to look at the links above and familiarize yourself with _GenBank_, _NCBI_, and the variety of resources _NCBI_ provides.**  

**Go back to the NCBI webpage and search for “Equus” (the genus for horses, zebras, donkeys and asses).** By default, the search option is set to “All Databases”. You can ask it to select an individual database, but for now, just leave it on “All Databases”. You should now see the number of matches returned for each database. Try clicking on a few of the databases to see what data are available for _Equus_.  

For this course we will primarily rely on data found in the _Nucleotide_ and _PopSet_ databases, but also use the _Taxonomy_ and _PubMed/PubMed Central_ databases. **Click on the PubMed and PubMed Central links and check out what kind of articles are available for _Equus_**.

While _PubMed_ provides listings and summaries of many articles, far more thorough searches can be done through databases such as _Web of Science_, which can be accessed through the “Database Finder” link on the UChicago Library website. (If you have difficulty figuring out how to use _Web of Knowledge_/_Web of Science_ please ask me.)

#### **Look at what data are available for _Equus_ in the _Nucleotide_ database**

1. How many nucleotide sequences are available in the _Nucelotide_ database?

2. Are there multiple genes that have been sequenced?

3. Look at "Top Organisms" (upper right bar). Are all the sequences from _Equus_?

4. If not, do you have any idea why they were returned as matches?


Most of these records represent a single, relatively short, DNA sequence. However, all published sequence data is deposited in _GenBank_ and this includes other kinds of sequences. **Do a search for “_Equus_ mitochondrion” and see what comes up**.

Each sequence has a unique _accession number_ (this is a unique code assigned to each sequence - they usually begin with 1-2 letters and are followed by 5-6 numbers).

**Try searching the accession number "U70192"**.

This is a complete sequence of the alpha 2 hemoglobin gene in _Equus greyi_ (Grevy's Zebra - one of the zebra's from today's reading).

---
##### cds = coding sequence
Notice the phrase "complete cds" at the end of the defintion. This tells us the record contains the complete coding sequence of the gene, versus "partial cds".

---

After the definition, you should see an abundance of information, including the organism’s name and taxonomic information, the names of the authors who published the paper containing this sequence along with the citation for their article, etc., and last, the sequence.  

1. How long is the sequence?
2. Is the sequence from a protein-coding gene?
3. What happens if you click on the "CDS" link?
4. Is base 167 of the unedited sequence a first, second, or third position codon? How do you know?

This is a complete cds, but remember that the first base in _partial cds_ records in _GenBank_ may not be the first base of the coding sequence.

We can also see the name of the product that is produced by these exons, as well as a translation of the protein itself (a string of letters coding for individual amino acids – MVLSAADKTNV…).  Notice that the protein sequence has its own _GenBank_ record with some more detailed information (where it says “protein_id=”).


### Using Search Limits

Let's go back to the search you did for _Equus_ earlier in the lab. Recall that many of the sequences returned were not from _Equus_ species.

1. Click on "Advanced" under the search field within the _Nucleotide_ database.
2. Select "Organism" from the Search Builder options.
3. Again, type in "_Equus_", click "Search", and look at your results.
4. Are all of the _Equus_ sequences? How many are there?

There are many helpful filters among the advanced search options. Be sure to check these out if you ever have trouble finding the sequence you want.
Continue to familiarize yourself with some of the other specifiers and/or perform searches for what data are available for some of your favorite organisms.

---
##### Accession Specifiers

The “Accession” specifier can be very useful when you want to access a range of accession numbers. You can enter two accession numbers with a colon in between to get all of the sequences within the given range. (You could try “U70192:U70194”). Sequences published together in a paper are usually given groups of numbers.

---

### Downloading Sequences from _GenBank_

<img src="https://i.ytimg.com/vi/uXdzuz5Q-hs/maxresdefault.jpg" width="500" height="10">

There is an unbelievable amount of data in _GenBank_, and a lot of great biology can be done using these publicly available sequences (you will do some of this great biology for your final projects). But how do you get this data in a usable format? We want to expedite the process as much as possible, rather than waste a bunch of time reformatting sequence files!

Return again to the “_Equus_” advanced search results. Let’s say we want to download just the sequences from _Equus kiang_ (the largest of the wild asses).

1. Find _Equus kiang_ in the "Top Organism" list, and select it.
2. Click the "Send to" link (upper right) and choose "File".
3. Select the "FASTA" format.

The sequences will be saved under the name “sequence.fasta” in Downloads.  You should be able to open this with TextEdit, TextWrangler, Emacs, Pico, or some other text editor. 

##### FASTA format
FASTA is a format that is read by many alignment programs and is a useful way to move data around.  In FASTA format, the first line of a sequence begins with a  “>” followed by the sequence name, and subsequent lines contain the sequence itself.  A line break separates each sequence from the name of the next sequence.

---

##### IMPORTANT NOTE
_**<u>Never</u>**_ open these files in MS Word or any other fancy word processor. Always use a simple text editor – this is because gaps and other extra formatting features can be added to data files if opened, edited and saved in MS Word, causing problems later.

---

Downloading all of the sequences from a search like this is usually not very useful but is a good way to start. You may need to filter through these sequences to get the ones you want.  

Say you want just a couple of the sequences retrieved. Go back to the search results for _Equus_ where many sequences are listed, along with their accessions. 


1. There are little open boxes to the left of each sequence. Tick a few of these.
2. Go to the tab that says “Send to” near the top and select “Clipboard”
3. Now click on the tab that appeared at the top that says “Clipboard”
3. Click "Add to clipboard"
4. Send sequences to file just as you did earlier


The Clipboard is useful if you want to include sequences from many different searches. 

#### Using the _Taxonomy Browser_ to Download Sequences

Using the _Taxonomy Browser_ is a more precise way of obtaining available sequences for a given lineage in _GenBank_. Specifically, it is a useful way to get rid of any sequences that are from other organisms associated with your target organism. For instance, if you search “cow”, you may find bacterial sequences in the results, perhaps because researchers isolated these bacteria from cows.  If “cow” is listed in any of the sequence information, it will be returned as a match. The _Taxonomy Browser_ can be used to avoid this problem.

Go back to the _NCBI_ webpage (http://www.ncbi.nlm.nih.gov/) and click on the “Resources” dropdown list at the top of the page. Navigate to “>Taxonomy>Taxonomy Browser”.  Take some time and explore the _Taxonomy Browser_.


1. Can you find your favorite organism? What is it?
2. Do other species within this genus/family have sequences available?

One useful feature is to check the “Nucleotide” box and click the “Display” button.  This will list the number of sequences available for each taxon (strain, species, genus, family, etc.). Now, to download all of the sequences from a taxon, you can click on the number to the right of that taxon to open a window with all of the sequences listed.  You can then check the boxes for all of the sequences you’d like to download and add them to the Clipboard.

#### Using the _PopSet_ Database to Download Sequences
Go back to the _NCBI_ webpage and **search the _PopSet_ database for “Laurasiatheria zinc finger”**. 

Find this result: “_Laurasiatheria zinc finger protein 691 (ZNF691) gene, partial cds_”. You can see the study that the sequences came from and a list of all of the sequences. However, this will only give you the sequences of a single gene used in the study and the title of this study starts with “Phylogenomic” which makes it sound like it used more than a single gene. **Click on the “Citation” link under the study title**. This brings you to the _PubMed_ record for the study where you can read the abstract. If you want all of the data from the study then you can click on the “Nucleotide” link under “Related Information” on the right. This returns all of the sequences from this study.  You can also search _PopSet_ for this specific study and get all sequences from each gene.

Sometimes alignments are uploaded as a part of the dataset and are displayed along with the sequences. If not, you can quickly align the dataset using _BLAST_ by clicking “Run BLAST alignment” under “Analyze this data set”. But we will talk about sequence alignment next week.


##### Important Note
When searching by citations in _NCBI_, only sequences from that particular study will appear in the record, not sequences from previous studies used in their phylogeny.

---

#### Basic Local Alignment Search Tool (_BLAST_) Searches

_BLAST_ (http://blast.ncbi.nlm.nih.gov/) is an incredibly useful tool (with a great name).  It compares nucleotide or protein sequences to a number of genomes, or to nucleotide and protein databases and return the closest matches, using one of three search algorithms.  

The “nucleotide blast” option is probably the most commonly used when working with DNA data. For more information on which _BLAST_ program to choose, click the “more…” button near the top of the page, which will download a PDF with detailed information.

Once on the _BLAST_ screen, you can paste a sequence in, enter an accession number or upload a file containing a sequence.  Let’s look at the zebra sequence we examined from the above exercise (Acc. #U70192).


1. Enter the accession number in the first box.
2. Select the database to search (usually "Other" works fine).
3. Choose one of the _BLAST_ algorithms, and hit "BLAST".

A number of sequences will be returned.  These matches are color-coded by the degree of similarity.  Scroll down and you can see the individual sequences, along with a number of statistics describing the closeness of the match.  The higher the coverage and the lower the E-value, the closer the match. See the link on the top-right of the page for more information.

1. What is the closest match? What is the second closest match?

The _BLAST_ suite of software can also be run locally on your own machine against either a pre-made database or against a custom database that you create yourself. This can be very useful for a number of different applications (e.g. to identify large numbers of sequences).

### Accessing Data From _TreeBASE_


<img src="http://treebase.org/treebase-web/images/logo.gif" width="700" height="10">

_TreeBASE_ (http://www.treebase.org/treebase-web/home.html) is a searchable website that retains alignments and trees from published studies (using both phenotypic and sequence data).  Unfortunately, most journals to do not require authors to deposit their alignments and trees here, and _TreeBASE_ can be a little more difficult to use than _GenBank_. However, its potential usefulness makes it an important resource to understand.

1. Click on the “Search TreeBASE” button on the left.
2. Click on the “Studies” tab and search for “_Equus_” in "All text".
4. Look at different studies that include _Equus_ species (e.g. Zhou et al. 2011)
5. Click on a study, and see the available files for download at the bottom
6. Search for your favorite organism (e.g. “Formicidae”) to see what is available.

What do you find?


You should become familiar with how to download the tree and alignment files.

### Accessing Data from _Morphobank_

_MorphoBank_ (http://www.morphobank.org/) is somewhat similar to _TreeBase_ in that it contains data matrices from published studies.  It also contains images that can be used for morphological analyses.  There are not very many matrices available, but hopefully this will increase in the future.

Go to the webpage and read more about _Morphobank_ and begin exploring some of the datasets that are available. Try viewing some of the matrices that are available (click on the “Browse Projects” option on top of the page to view a listing of projects).  

---

### Assignment

**Use the Web of Science to find this paper**:

Gómez-Acevedo S, Ruci-Arce L, Delgado-Salinas A, Magallón S, Equiarte LE. 2010. Neotropical mutualism between _Acacia_ and _Pseudomyrmex_: Phylogeny and divergence times. _Mol Phylogenet Evol_. 56:393-408.

This study examines coevolution between acacias and their mutualistic ant parters in the Neotropics. These ants nest in acacia plants and protect them from herbivores, pathogenic fungi, and encroaching vegetation.

In future labs, we are going to reanalyze the dataset of Gómez-Acevedo. Therefore, you should use _GenBank_ to download all ant sequences (Table 2, not Table 1) in FASTA format. Do not download the plant sequences. You should create two files: one for each gene used in the study (Long-Wave Rhodopsin (LW Rh) and Wingless (Wg)). Each file should have a single sequence from each species used in the study. Remember, the correct formatting of the sequences is essential for them to be read by an analysis program so only use simple text editors and <u>never</u> use Microsoft Word to view the files.

**THIS NEEDS WORK**

Provide sequences or explain how to download them

---


## Additional Websites of Interest

Open Tree of Life: http://www.opentreeoflife.org

Encyclopedia of Life: http://www.eol.org/

Morphbank: http://www.morphbank.net/

Angiosperm Phylogeny Website: http://www.mobot.org/MOBOT/research/APWeb/

Antweb: http://www.antweb.org/

Hymenoptera Genome Database: http://hymenopteragenome.org/

Fishbase: http://www.fishbase.org/search.php

Mycobank: http://www.mycobank.org/

LIAS: http://www.lias.net/



### What About All Those Genomes I Keep Hearing About?

This is completely supplementary so only do it if you have some extra time!

With recent developments in DNA sequencing technology, the number of whole genomes available has drastically increased. For some research, you may not want just a short sequence or gene, but an entire genome. However, finding the right place to download these sequences can be confusing, particularly if they are not from model organisms.

1. Return to the _NCBI_ homepage, and click “Genome” under “Popular Resources”.
2. There are several options, but click “Browse by Organism”. 
3. Now search for a cool genome using the Search function. “Formicidae” is cool.
4. Click on the name of one of the organisms (e.g. “Atta cephalotes”).
5. Click on the link for “Assembly” to access information on the assembly.
6. Click on the link for “WGS Project” to see information on the genome project.
7. At the bottom, you can click on the link for “WGS_SCAFLD” (or “WGS”).
8. Now you can download all of the genomic sequences as you did before (but **don't actually do this now**, as it's a big file!).


##### WGS = Whole Genome Shotgun

This phrase refers to the procedure of sequencing fragments of the genome at random and then using several algorithms to assemble these sequences into a whole genome. Specifically, these fragments are first assembled into _contigs_, which are known, contiguous sequences. Sometimes, _contigs_ are known to be a certain distance apart in the genome but the intervening sequence between these _contigs_ is unknown. These _contigs_ can be connected together into a single sequence by filling in the intervening sequence with unknown nucleotide characters (“N”). These longer, but N-rich sequences are _scaffolds_.

**Spend some time checking out the genomes that have been sequenced!**

<img src="http://www.alexanderwild.com/Ants/Taxonomic-List-of-Ant-Genera/Atta/i-krNfZqH/2/XL/cephalotes16-XL.jpg" width="500" height="5">

|_Atta cephalotes_|
|:---------:|
|Image Credit [Alexander Wild](www.alexanderwild.com)|
