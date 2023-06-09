Regular Expression in R
================
Hedia Tnani

## Regular Expressions

## **What are Regular Expressions?**

Regular expressions (`regex` or `regexp`) are patterns used to match and
manipulate text data. They are widely used in text processing and are
supported by many programming languages, including R.

Regex patterns consist of a combination of letters, digits, and special
characters that define the search criteria. The basic idea is to
describe a set of strings using a concise and flexible syntax. For
example, you could use a regex pattern to search for all email addresses
in a text file or to extract all dates from a web page.

Regular expressions are extremly useful to help clean data and parse
strings. They are very helpful in bioinformatics.

## Playing with regex

### **The `grep` Function**

The **`grep`** function in base R is used to search for patterns in a
character vector and returns the indices of the elements that contain
the pattern.

``` r
# create a character vector
words <- c("bioinformatics", "mathematics", "biology", "chemistry", "computer science")

# search for all elements that contain the letter "a"
grep("a", words)
```

    [1] 1 2

In this example, the **`grep`** function searches for all elements in
the **`words`** vector that contain the letter “a” and returns the
indices of the matching elements.

### **`sub` and `gsub` Functions**

The **`sub`** and **`gsub`** functions are used to replace a pattern in
a string with another pattern. The **`sub`** function replaces the first
occurrence of the pattern, while the **`gsub`** function replaces all
occurrences of the pattern.

``` r
# create a character vector
text <- "Mathematics is very difficult sometimes. Mathematics is not easy for biologists to understand"

# replace the word "Mathematics" with "Bioinformatics"
sub("Mathematics", "Bioinformatics", text)
```

    [1] "Bioinformatics is very difficult sometimes. Mathematics is not easy for biologists to understand"

``` r
# replace all occurrences of the word "is" with "IS"
gsub("is", "IS", text)
```

    [1] "Mathematics IS very difficult sometimes. Mathematics IS not easy for biologISts to understand"

In the example above, the **`sub`** function replaces the **first**
occurrence of the word “Mathematics” with “Bioinformatics” in the
**`text`** vector. In the second example, the **`gsub`** function
replaces all occurrences of the word “is” with “IS” in the **`text`**
vector.

``` r
# search for all elements that contain "Mathematics" or "biologISts"
grep("(Mathematics|biologISts)", text)
```

    [1] 1

### **The `grep` Function**

The **`grepl()`** function returns a logical vector indicating whether
each element of **`x`** matches the pattern or regular expression. If a
match is found, the corresponding element of the output vector will be
**`TRUE`**; otherwise, it will be **`FALSE`**.

``` r
# search for all elements that contain the letter "a" and returns a logical 
grepl("a", words)
```

    [1]  TRUE  TRUE FALSE FALSE FALSE

`grepl` returns a logical vector of the same length as **`words`**, with
**`TRUE`** values for elements that contain the letter “a” and
**`FALSE`** values for elements that do not.

### The `str_subset` function

`str_subset()` from the `stringr` package returns all elements of
`string` where there’s at least one match to `pattern`. It’s a wrapper
around `x[str_detect(x, pattern)]`, and is equivalent to
`grep(pattern, x, value = TRUE).`

``` r
library(stringr)
```


    Attaching package: 'stringr'

    The following object is masked _by_ '.GlobalEnv':

        words

``` r
str_subset(text, "(Mathematics|biologISts)")
```

    [1] "Mathematics is very difficult sometimes. Mathematics is not easy for biologists to understand"

Let’s get the `words` starting with `b` from the previous `words`
object. **`"^b"`** is a regular expression pattern that matches any
string that starts with “b”.

``` r
str_subset(words, "^b")
```

    [1] "bioinformatics" "biology"       

When you call **`str_subset(words, "^b")`**, the function searches each
string in **`words`** for the pattern **`"^b"`**, and returns only the
strings that start with “b”. The result is a new character vector that
includes only the matching strings.

``` r
str_subset(words, "^b", negate = T)
```

    [1] "mathematics"      "chemistry"        "computer science"

Setting **`negate = TRUE`** in the function call will return all
elements of the character vector that do not match the specified regular
expression, in this case, all elements that do not start with the letter
“b”.

Let’s see another example.

Let’s get the `words` ending with `s` from the previous `words` object.
**`"s$"`** is a regular expression pattern that matches any string that
ends with “s”.

``` r
str_subset(words, "s$")
```

    [1] "bioinformatics" "mathematics"   

When you call **`str_subset(words, "s$")`**, the function searches each
string in **`words`** for the pattern **`"s$"`**, and returns only the
strings that end with “s”. The result is a new character vector that
includes only the matching strings.

Let’s get the `words` that matches either “c” or “s”.

``` r
str_subset(words, "[cs]")
```

    [1] "bioinformatics"   "mathematics"      "chemistry"        "computer science"

**`str_subset(words, "[cs]")`** will return all strings in **`words`**
that contain either “c” or “s”.

### The **`gregexpr()`** function

**`gregexpr()`** is a base R function that searches for matches of a
regular expression in a character string and returns the position of the
matches. It can be used to find all occurrences of a pattern in a
string, even if there are multiple matches.

``` r
library(Biostrings)

# Create a DNAStringSet object
dna_strings <- DNAStringSet(c("ACGTAGTTCAAGTACAGATCGGGGGCTAGCCCCCTAGCTAGCT", "ACGTATTTTGTACAGATAAAACGCTAGCTAGCTAGCTAGATCGGGGGC"))

# Set names for the sequences
names(dna_strings) <- c("Sequence1", "Sequence2")

dna_strings
```

    DNAStringSet object of length 2:
        width seq                                               names               
    [1]    43 ACGTAGTTCAAGTACAGATCGGGGGCTAGCCCCCTAGCTAGCT       Sequence1
    [2]    48 ACGTATTTTGTACAGATAAAACGCTAGCTAGCTAGCTAGATCGGGGGC  Sequence2

**`gregexpr`** finds all matches for the regular expression “TA” in each
of the sequences.

``` r
# Use gregexpr to find matches for a regular expression in the DNAStringSet
matches <- gregexpr("TA", dna_strings)
matches
```

    [[1]]
    [1]  4 13 27 35 39
    attr(,"match.length")
    [1] 2 2 2 2 2
    attr(,"index.type")
    [1] "chars"
    attr(,"useBytes")
    [1] TRUE

    [[2]]
    [1]  4 11 17 25 29 33 37
    attr(,"match.length")
    [1] 2 2 2 2 2 2 2
    attr(,"index.type")
    [1] "chars"
    attr(,"useBytes")
    [1] TRUE

The output of **`gregexpr`** is a list where each element corresponds to
one of the input sequences. For each sequence, **`gregexpr`** returns a
vector of the positions where matches were found, along with the length
of each match. If no matches were found for a sequence, **`gregexpr`**
returns an empty integer vector.

``` r
# Find the position of the pattern "GGGGG" using base R
unlist(gregexpr("G{5}", dna_strings))
```

    [1] 21 43

You can use the **`regmatches()`** function along with **`gregexpr()`**
.

``` r
# Use gregexpr and regmatches to extract matched sequences
matched_seqs <- regmatches(dna_strings, gregexpr("G{5}", dna_strings))

# View the matched sequences
matched_seqs
```

    $Sequence1
    [1] "GGGGG"

    $Sequence2
    [1] "GGGGG"

The **`gregexpr()`** function is used to identify the positions of the
matches, and **`regmatches()`** is used to extract the matched sequences
based on those positions. The resulting object is a list where each
element corresponds to the matched sequence(s) for a sequence in the
**`DNAStringSet`**.

## Use case: SCHIZOPHRENIA

In this tutorial, we will explore how to use regular expressions in R
using base R and tidyverse packages to analyze data from the `rentrez`
package on schizophrenia.

## **Getting the Data**

``` r
# install and load rentrez package
#install.packages("rentrez")

# load the library
library(rentrez)

# search for articles related to schizophrenia
schizo_search <- entrez_search(db="pubmed", term="schizophrenia")

# get the ids for the top 20 articles
schizo_ids <- entrez_fetch(db="pubmed", id=schizo_search$ids[1:20], rettype="xml", parsed = T)
class(schizo_ids)
```

    [1] "XMLInternalDocument" "XMLAbstractDocument"

``` r
schizo_list <- XML::xmlToList(schizo_ids)
#schizo_list
```

``` r
schizo_list[[1]]$MedlineCitation$Article$ArticleTitle
```

    [1] "Hospital spending and length of hospital stay for mental disorders in Hunan, China."

**`schizo_list`** is a list of nested objects containing MedlineCitation
information, you can use **`map`** from the **`purrr`** package to
extract all the ArticleTitles from each nested list.

``` r
library(purrr)
```


    Attaching package: 'purrr'

    The following object is masked from 'package:XVector':

        compact

    The following object is masked from 'package:IRanges':

        reduce

``` r
# Use map with .x to extract the ArticleTitle from each nested list
titles <- map(schizo_list, ~ .x$MedlineCitation$Article$ArticleTitle)
titles
```

    $PubmedArticle
    [1] "Hospital spending and length of hospital stay for mental disorders in Hunan, China."

    $PubmedArticle
    [1] "Hepatoprotective Effect of Ethanolic Extract of Garlic Against Reserpine Induced Toxicity in Wistar Rats."

    $PubmedArticle
    [1] "Real-life functioning in women with schizophrenia living in residential facilities: Gender-based comparison."

    $PubmedArticle
    [1] "Editorial: Treatment resistance in psychotic disorders."

    $PubmedArticle
    [1] "Skills program for awareness, connectedness, and empowerment: A conceptual framework of a skills group for individuals with a psychosis-risk syndrome."

    $PubmedArticle
    [1] "Correction to: Understanding translational research in schizophrenia: A novel insight into animal models."

    $PubmedArticle
    [1] "Phenomenology of auditory verbal hallucination in schizophrenia: An erroneous perception or something else?"

    $PubmedArticle
    [1] "Functional network interactions in patients with schizophrenia with persistent auditory verbal hallucinations: A multimodal MRI fusion approach using three-way pICA."

    $PubmedArticle
    [1] "Prevalence and gender distribution of excoriation (skin-picking) disorder: A systematic review and meta-analysis."

    $PubmedArticle
    [1] "Review of Major Social Determinants of Health in Schizophrenia-Spectrum Psychotic Disorders: III. Biology."

    $PubmedArticle
    [1] "Validation of the multiple disability multidimensional attitudes scale toward persons with disabilities."

    $PubmedArticle
    [1] "Review of Major Social Determinants of Health in Schizophrenia-Spectrum Disorders: II. Assessments."

    $PubmedArticle
    [1] "Review of Major Social Determinants of Health in Schizophrenia-Spectrum Psychotic Disorders: I. Clinical Outcomes."

    $PubmedArticle
    [1] "Impact of Tardive Dyskinesia on Physical, Psychological, Social, and Professional Domains of Patient Lives: A Survey of Patients in the United States."

    $PubmedArticle
    [1] "Attenuation of Stimulated Accumbal Dopamine Release by NMDA Is Mediated through Metabotropic Glutamate Receptors."

    $PubmedArticle
    [1] "Clinical and biological correlates of optical coherence tomography findings in schizophrenia."

    $PubmedArticle
    [1] "Schizo-Net: A novel Schizophrenia Diagnosis framework using late fusion multimodal deep learning on Electroencephalogram-based Brain connectivity indices."

    $PubmedArticle
    [1] "Successful switching from risperidone to cariprazine in a schizophrenic patient with pronounced functional deficit. Case report."

    $PubmedArticle
    [1] "Application of machine learning in predicting aggressive behaviors from hospitalized patients with schizophrenia."

    $PubmedArticle
    [1] "The role of the salience network in cognitive and affective deficits."

## Using regular expression

``` r
# find the indices of titles that contain the word "schizophrenia"
grep("schizophrenia", titles)
```

    [1]  3  6  7  8 16 19

``` r
# find the indices of titles that contain the words "schizophrenia" or "psychosis"
grep("schizophrenia|psychosis", titles)
```

    [1]  3  5  6  7  8 16 19

Let’s subset the `titles` object:

``` r
# find the titles that contain the word "schizophrenia"
titles[grep("schizophrenia", titles)]
```

    $PubmedArticle
    [1] "Real-life functioning in women with schizophrenia living in residential facilities: Gender-based comparison."

    $PubmedArticle
    [1] "Correction to: Understanding translational research in schizophrenia: A novel insight into animal models."

    $PubmedArticle
    [1] "Phenomenology of auditory verbal hallucination in schizophrenia: An erroneous perception or something else?"

    $PubmedArticle
    [1] "Functional network interactions in patients with schizophrenia with persistent auditory verbal hallucinations: A multimodal MRI fusion approach using three-way pICA."

    $PubmedArticle
    [1] "Clinical and biological correlates of optical coherence tomography findings in schizophrenia."

    $PubmedArticle
    [1] "Application of machine learning in predicting aggressive behaviors from hospitalized patients with schizophrenia."

``` r
# find the titles that contain the words "schizophrenia" or "psychosis"
titles[grep("schizophrenia|psychosis", titles)]
```

    $PubmedArticle
    [1] "Real-life functioning in women with schizophrenia living in residential facilities: Gender-based comparison."

    $PubmedArticle
    [1] "Skills program for awareness, connectedness, and empowerment: A conceptual framework of a skills group for individuals with a psychosis-risk syndrome."

    $PubmedArticle
    [1] "Correction to: Understanding translational research in schizophrenia: A novel insight into animal models."

    $PubmedArticle
    [1] "Phenomenology of auditory verbal hallucination in schizophrenia: An erroneous perception or something else?"

    $PubmedArticle
    [1] "Functional network interactions in patients with schizophrenia with persistent auditory verbal hallucinations: A multimodal MRI fusion approach using three-way pICA."

    $PubmedArticle
    [1] "Clinical and biological correlates of optical coherence tomography findings in schizophrenia."

    $PubmedArticle
    [1] "Application of machine learning in predicting aggressive behaviors from hospitalized patients with schizophrenia."

``` r
# Search for articles on "schizophrenia" that mention "antipsychotic" in the title
search <- "schizophrenia AND antipsychotic[TIAB]"
abstracts <- entrez_search(db = "pubmed", term = search, retmax = 10)$ids
schizoAntipsy <- entrez_fetch(db = "pubmed", id = abstracts, rettype = "xml", parsed = T) 
schizoAntipsy_list <- XML::xmlToList(schizoAntipsy)
titles <- map(schizoAntipsy_list, ~ .x$MedlineCitation$Article$ArticleTitle)
titles
```

    $PubmedArticle
    [1] "Impact of Tardive Dyskinesia on Physical, Psychological, Social, and Professional Domains of Patient Lives: A Survey of Patients in the United States."

    $PubmedArticle
    [1] "Successful switching from risperidone to cariprazine in a schizophrenic patient with pronounced functional deficit. Case report."

    $PubmedArticle
    [1] "Olanzapine-induced nonalcoholic fatty liver disease: The effects of differential food pattern and the involvement of PGRMC1 signaling."

    $PubmedArticle
    [1] "Prospective, randomized, multicenter clinical trial evaluating longitudinal changes in brain function and microstructure in first-episode schizophrenia patients treated with long-acting injectable paliperidone palmitate versus oral antipsychotics."

    $PubmedArticle
    [1] "Assessment of Neuroanatomical Endophenotypes of Autism Spectrum Disorder and Association With Characteristics of Individuals With Schizophrenia and the General Population."

    $PubmedArticle
    [1] "Antipsychotic treatment patterns in refugees and their Swedish-born peers with first-episode non-affective psychosis: findings from the REMAIN study."

    $PubmedArticle
    [1] "Fibre density and fibre-bundle cross-section of the corticospinal tract are distinctly linked to psychosis-specific symptoms in antipsychotic-naïve patients with first-episode schizophrenia."

    $PubmedArticle
    [1] "Brain Structure Measurements Predict Individualized Treatment Outcome of 12-Week Antipsychotic Monotherapies in First-episode Schizophrenia."

    $PubmedArticle
    [1] "Reward disturbances in antipsychotic-naïve patients with first-episode psychosis and their association to glutamate levels."

    $PubmedArticle
    [1] "Striatum-related spontaneous coactivation patterns predict treatment response on positive symptoms of drug-naive first-episode schizophrenia with risperidone monotherapy."

``` r
# replace the word "schizophrenia" with "SZ" in each title
str_replace(titles, "(s|S)chizophrenia", "SZ")
```

     [1] "Impact of Tardive Dyskinesia on Physical, Psychological, Social, and Professional Domains of Patient Lives: A Survey of Patients in the United States."                                                                                      
     [2] "Successful switching from risperidone to cariprazine in a schizophrenic patient with pronounced functional deficit. Case report."                                                                                                            
     [3] "Olanzapine-induced nonalcoholic fatty liver disease: The effects of differential food pattern and the involvement of PGRMC1 signaling."                                                                                                      
     [4] "Prospective, randomized, multicenter clinical trial evaluating longitudinal changes in brain function and microstructure in first-episode SZ patients treated with long-acting injectable paliperidone palmitate versus oral antipsychotics."
     [5] "Assessment of Neuroanatomical Endophenotypes of Autism Spectrum Disorder and Association With Characteristics of Individuals With SZ and the General Population."                                                                            
     [6] "Antipsychotic treatment patterns in refugees and their Swedish-born peers with first-episode non-affective psychosis: findings from the REMAIN study."                                                                                       
     [7] "Fibre density and fibre-bundle cross-section of the corticospinal tract are distinctly linked to psychosis-specific symptoms in antipsychotic-naïve patients with first-episode SZ."                                                         
     [8] "Brain Structure Measurements Predict Individualized Treatment Outcome of 12-Week Antipsychotic Monotherapies in First-episode SZ."                                                                                                           
     [9] "Reward disturbances in antipsychotic-naïve patients with first-episode psychosis and their association to glutamate levels."                                                                                                                 
    [10] "Striatum-related spontaneous coactivation patterns predict treatment response on positive symptoms of drug-naive first-episode SZ with risperidone monotherapy."                                                                             

In this example, we use the **`str_replace`** function from
**`stringr`** to replace the word “schizophrenia” or “Schizophrenia”
with “SZ” in each title in the **`titles`** vector.

### **The `str_extract` Function**

**`str_extract()`** function extracts substrings that match the regular
expression pattern.

``` r
# extract the first word of each title
str_extract(titles, "\\w+")
```

     [1] "Impact"        "Successful"    "Olanzapine"    "Prospective"  
     [5] "Assessment"    "Antipsychotic" "Fibre"         "Brain"        
     [9] "Reward"        "Striatum"     

When typing regular expressions, there are a group of special characters
called metacharacters that have other functions. These are `.{()\^$|?*+`
.

The backslash is ***SUPER*** important because if we want to search for
any of these characters without using their built in function we must
escape the character with a backslash.

The backslash is a metacharacter too! So to create a backslash for the
function to search with, we need to escape the backslash!

Let´s extract the first digit (0-9) that appears in each element of the
**`titles`** character vector.

``` r
# extract the first digit of each title
str_extract(titles, "[:digit:]{1}")
```

     [1] NA  NA  "1" NA  NA  NA  NA  "1" NA  NA 

The code **`str_extract(titles, "[:digit:]{1}")`** extracts the first
digit (0-9) that appears in each element of the **`titles`** character
vector using the **`str_extract()`** function from the **`stringr`**
package.

The regular expression **`[:digit:]{1}`** matches a single digit
character. The curly braces **`{1}`** indicate that we want to match
exactly one digit character, and the square brackets **`[]`** are used
to define a character class. The **`[:digit:]`** character class matches
any digit character.

Thus, **`str_extract(titles, "[:digit:]{1}")`** will return a vector of
the first digit character found in each element of the **`titles`**
vector. If an element does not contain any digit characters, the result
will be an empty string for that element.

## Use case: Covid 19 and SARS-CoV-2

In January 2020, scientists deciphered the genome of the `SARS-CoV-2`
virus, which causes `Covid-19`. The sample came from a 41-year-old man
who worked at the Wuhan seafood market, where the first group of cases
appeared.

Researchers are now striving to make sense of this viral recipe, which
could inspire drugs, vaccines, and other tools to fight the current
pandemic.

As bioinformaticians, we want to study the different genomes and
proteomes of `SARS-CoV-2`, which causes `Covid-19`.

SARS-CoV-2 The genome of the new coronavirus is a sequence of 30,000
base pairs. (The human genome has more than 3 billion). Scientists have
identified the genes for no less than 29 proteins, which perform a whole
range of tasks from making copies of the coronavirus to suppressing the
body’s immune responses.

<img
src="https://static01.nyt.com/newsgraphics/2020/04/02/virus-genome/assets/images/coronavirus-cutaway-600.png"
width="300" />

[Source](https://static01.nyt.com/newsgraphics/2020/04/02/virus-genome/assets/images/coronavirus-cutaway-600.png)

With the availability of the COVID-19 genome publicly available on the
National Center for Biotechnology Information (NCBI) website, we are
interested in exploring the genome of SARS-CoV-2 in the first place.
Let’s start by installing the library we will use to search the NCBI
database.

## Getting the data

First, you can use `entrez_dbs()` to find the list of available
databases:

``` r
library(rentrez)
entrez_dbs()
```

     [1] "pubmed"          "protein"         "nuccore"         "ipg"            
     [5] "nucleotide"      "structure"       "genome"          "annotinfo"      
     [9] "assembly"        "bioproject"      "biosample"       "blastdbinfo"    
    [13] "books"           "cdd"             "clinvar"         "gap"            
    [17] "gapplus"         "grasp"           "dbvar"           "gene"           
    [21] "gds"             "geoprofiles"     "homologene"      "medgen"         
    [25] "mesh"            "nlmcatalog"      "omim"            "orgtrack"       
    [29] "pmc"             "popset"          "proteinclusters" "pcassay"        
    [33] "protfam"         "pccompound"      "pcsubstance"     "seqannot"       
    [37] "snp"             "sra"             "taxonomy"        "biocollections" 
    [41] "gtr"            

There is a set of functions with names starting `entrez_db_` that can be
used to gather more information about each of these databases:

**Functions that help you learn about NCBI databases**

<table>
<colgroup>
<col style="width: 33%" />
<col style="width: 66%" />
</colgroup>
<thead>
<tr class="header">
<th>Function name</th>
<th>Return</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><code>entrez_db_summary()</code></td>
<td>Brief description of what the database is</td>
</tr>
<tr class="even">
<td><code>entrez_db_searchable()</code></td>
<td>Set of search terms that can used with this database</td>
</tr>
<tr class="odd">
<td><code>entrez_db_links()</code></td>
<td>Set of databases that might contain linked records</td>
</tr>
</tbody>
</table>

As a bioinformatician, we want to find the first 150 records in the
nucleotide database that match
**`Severe acute respiratory syndrome coronavirus 2`**. How many records
in the nucleotide sequence database match
**`Severe acute respiratory syndrome coronavirus 2`**?

``` r
covid <- "Severe acute respiratory syndrome coronavirus 2"
covid_nuc <- entrez_search(db="nuccore", term=covid,retmax = 30)
covid_nuc$count
```

    [1] 6820940

What’s the structure of the `covid_nuc` object?

``` r
str(covid_nuc)
```

    List of 5
     $ ids             : chr [1:30] "2470956531" "2470956518" "2470956505" "2470956492" ...
     $ count           : int 6820940
     $ retmax          : int 30
     $ QueryTranslation: chr "\"Severe acute respiratory syndrome coronavirus 2\"[Organism] OR Severe acute respiratory syndrome coronavirus 2[All Fields]"
     $ file            :Classes 'XMLInternalDocument', 'XMLAbstractDocument' <externalptr> 
     - attr(*, "class")= chr [1:2] "esearch" "list"

How to get the `FASTA` sequence of the nucleotide sequences of the above
`covid_nuc$ids` ?

By providing a vector of IDs in **`covid_nuc$ids`**, the
**`entrez_fetch()`** function retrieves the corresponding nucleotide
sequences in FASTA format from the NCBI Nucleotide database.

``` r
covid_n <- entrez_fetch(db="nuccore", id=covid_nuc$ids, rettype="fasta")
nchar(covid_n)
```

    [1] 910043

Congratulations, now you have a really huge character vector! Rather
than printing all those thousands of bases we can take a peak at the top
of the file:

``` r
cat(strwrap(substr(covid_n, 1, 500)), sep="\n")
```

    >OQ711802.1 Severe acute respiratory syndrome coronavirus 2 isolate
    SARS-CoV-2/human/JPN/HiroFS034/2022, complete genome
    AACCAACCAACTTTCGATCTCTTGTAGATCTGTTCTCTAAACGAACTTTAAAATCTGTGTGGCTGTCACT
    CGGCTGCATGCTTAGTGCACTCACGCAGTATAATTAATAACTAATTACTGTCGTTGACAGGACACGAGTA
    ACTCGTCTATCTTCTGCAGGCTGCTTACGGTTTCGTCCGTGTTGCAGCCGATCATCAGCACATCTAGGTT
    TTGTCCGGGTGTGACCGAAAGGTAAGATGGAGAGCCTTGTCCCTGGTTTCAACGAGAAAACACACGTCCA
    ACTCAGTTTGCCTGTTTTACAGGTTCGCGACGTGCTCGTACGTGGCTTTGGAGACTCCGTGGAGGAGGTC
    TTATCAGAGGCACGTCAACATCTT

If we wanted to use these sequences in some other application we could
write them to file:

``` r
write(covid_n, "covid_nuc.fasta")
```

Alternatively, if you want to use them within an R session  
we could write them to a temporary file then read that.

``` r
temp <- tempfile()
write(covid_n, temp)
covid_n1 <- readDNAStringSet(temp)
head(names(covid_n1))
```

    [1] "OQ711802.1 Severe acute respiratory syndrome coronavirus 2 isolate SARS-CoV-2/human/JPN/HiroFS034/2022, complete genome"
    [2] "OQ711801.1 Severe acute respiratory syndrome coronavirus 2 isolate SARS-CoV-2/human/JPN/HiroFS033/2022, complete genome"
    [3] "OQ711800.1 Severe acute respiratory syndrome coronavirus 2 isolate SARS-CoV-2/human/JPN/HiroFS032/2022, complete genome"
    [4] "OQ711799.1 Severe acute respiratory syndrome coronavirus 2 isolate SARS-CoV-2/human/JPN/HiroFS027/2022, complete genome"
    [5] "OQ711798.1 Severe acute respiratory syndrome coronavirus 2 isolate SARS-CoV-2/human/JPN/HiroFS024/2022, complete genome"
    [6] "OQ711797.1 Severe acute respiratory syndrome coronavirus 2 isolate SARS-CoV-2/human/JPN/HiroFS023/2022, complete genome"

### Hands -on Exercice For NUcleotide Sequences

We want to get the different identifiers that are before the `\\s` and
the word `Severe`. Which regular expression should we use?

1.  Using `str_extract` and `^` and `.` and `{1,10}`

    ``` r
    str_extract(names(covid_n1), "^.{1,10}")
    ```

         [1] "OQ711802.1" "OQ711801.1" "OQ711800.1" "OQ711799.1" "OQ711798.1"
         [6] "OQ711797.1" "OQ711796.1" "OQ711795.1" "OQ711794.1" "OQ711793.1"
        [11] "OQ711792.1" "OQ711791.1" "OQ711790.1" "OQ711789.1" "OQ711788.1"
        [16] "OQ711735.1" "OQ711734.1" "OQ711733.1" "OQ711732.1" "OQ711731.1"
        [21] "OQ711730.1" "OQ711729.1" "OQ711728.1" "OQ711727.1" "OQ711726.1"
        [26] "OQ711725.1" "OQ711724.1" "OQ711723.1" "OQ711722.1" "OQ711721.1"

The **`^`** anchor specifies the start of the string. The **`.`** is a
wildcard character that matches any character. The **`{1,10}`**
quantifier specifies that the previous element (i.e., the **`.`**) can
occur between 1 and 10 times, inclusive. In other words, this regular
expression will match any string that is between 1 and 10 characters
long.

1.  Using `str_extract` and `^\\S*`

    ``` r
    str_extract(names(covid_n1),"^\\S*")
    ```

         [1] "OQ711802.1" "OQ711801.1" "OQ711800.1" "OQ711799.1" "OQ711798.1"
         [6] "OQ711797.1" "OQ711796.1" "OQ711795.1" "OQ711794.1" "OQ711793.1"
        [11] "OQ711792.1" "OQ711791.1" "OQ711790.1" "OQ711789.1" "OQ711788.1"
        [16] "OQ711735.1" "OQ711734.1" "OQ711733.1" "OQ711732.1" "OQ711731.1"
        [21] "OQ711730.1" "OQ711729.1" "OQ711728.1" "OQ711727.1" "OQ711726.1"
        [26] "OQ711725.1" "OQ711724.1" "OQ711723.1" "OQ711722.1" "OQ711721.1"

    This `\\S*` regular expression matches a sequence of zero or more
    non-whitespace characters at the beginning of a string.

2.  Using `str_extract` and `^([\\S]+)`

    ``` r
    str_extract(names(covid_n1),"^([\\S]+)")
    ```

         [1] "OQ711802.1" "OQ711801.1" "OQ711800.1" "OQ711799.1" "OQ711798.1"
         [6] "OQ711797.1" "OQ711796.1" "OQ711795.1" "OQ711794.1" "OQ711793.1"
        [11] "OQ711792.1" "OQ711791.1" "OQ711790.1" "OQ711789.1" "OQ711788.1"
        [16] "OQ711735.1" "OQ711734.1" "OQ711733.1" "OQ711732.1" "OQ711731.1"
        [21] "OQ711730.1" "OQ711729.1" "OQ711728.1" "OQ711727.1" "OQ711726.1"
        [26] "OQ711725.1" "OQ711724.1" "OQ711723.1" "OQ711722.1" "OQ711721.1"

    This regular expression will match the beginning of a string and
    capture all non-whitespace characters following it. The captured
    characters will be stored in the first capture group.

    `([\\S]+)` is a capture group that matches one or more
    non-whitespace characters. The square brackets and the \S inside
    them are a character class that matches any non-whitespace
    character.

3.  Using `str_extract`

    and `[:alpha:]` and `[:digit:]` and `[:punct:]`

    ``` r
    str_extract(names(covid_n1), "[:alpha:]{2}+[:digit:]{6}+[:punct:]+[:digit:]")
    ```

         [1] "OQ711802.1" "OQ711801.1" "OQ711800.1" "OQ711799.1" "OQ711798.1"
         [6] "OQ711797.1" "OQ711796.1" "OQ711795.1" "OQ711794.1" "OQ711793.1"
        [11] "OQ711792.1" "OQ711791.1" "OQ711790.1" "OQ711789.1" "OQ711788.1"
        [16] "OQ711735.1" "OQ711734.1" "OQ711733.1" "OQ711732.1" "OQ711731.1"
        [21] "OQ711730.1" "OQ711729.1" "OQ711728.1" "OQ711727.1" "OQ711726.1"
        [26] "OQ711725.1" "OQ711724.1" "OQ711723.1" "OQ711722.1" "OQ711721.1"

This regular expression can be broken down as follows:

-   **`[:alpha:]{2}`**: Matches any two alphabetic characters.

-   **`+`**: The **`+`** sign means that the preceding pattern should be
    matched one or more times. In this case, it applies to the
    **`[:alpha:]{2}`** pattern.

-   **`[:digit:]{6}`**: Matches any six numeric characters.

-   **`+`**: Similar to the previous use, the **`+`** sign applies to
    the **`[:digit:]{6}`** pattern.

-   **`[:punct:]+`**: Matches one or more punctuation characters.

-   **`[:digit:]`**: Matches a single numeric character.

Overall, this regular expression matches a string that contains two
alphabetic characters, followed by six numeric characters, followed by
one or more punctuation characters, and finally a single numeric
character.

1.  Using the `Positive Lookahead`

Lookarounds are helpful to reduce complicated regex expressions and make
it easier to identify what we want to find in a string.

There are two types of lookarounds: `lookaheads`and `lookbehinds`. In
this solution we’ll be using the `Positive Lookahead` `(?=pattern)`

``` r
str_extract(names(covid_n1), ".*(?=\\sSevere)")
```

     [1] "OQ711802.1" "OQ711801.1" "OQ711800.1" "OQ711799.1" "OQ711798.1"
     [6] "OQ711797.1" "OQ711796.1" "OQ711795.1" "OQ711794.1" "OQ711793.1"
    [11] "OQ711792.1" "OQ711791.1" "OQ711790.1" "OQ711789.1" "OQ711788.1"
    [16] "OQ711735.1" "OQ711734.1" "OQ711733.1" "OQ711732.1" "OQ711731.1"
    [21] "OQ711730.1" "OQ711729.1" "OQ711728.1" "OQ711727.1" "OQ711726.1"
    [26] "OQ711725.1" "OQ711724.1" "OQ711723.1" "OQ711722.1" "OQ711721.1"

This regular expression consists of two parts:

1.  **`.*`**: a greedy quantifier that matches any character (except for
    newline characters) zero or more times, as many times as possible.

2.  **`(?=\\sSevere)`**: a positive lookahead that matches a space
    character followed by the word “Severe”, but does not include it in
    the match.

Together, this regular expression matches any characters (except for
newlines) that occur immediately before the phrase “Severe” preceded by
a space character. The positive lookahead **`(?=\\sSevere)`** ensures
that the match ends right before the space character that precedes
“Severe”, so that “Severe” itself is not included in the match.

### Hands -on Exercice For Amino-Acid Sequences

Let’s get the data

``` r
covid_prot <- entrez_search(db="protein", term=covid,retmax = 30)
covid_p <- entrez_fetch(db="protein", id=covid_prot$ids, rettype="fasta")
nchar(covid_p)
```

    [1] 31873

Same as before

``` r
cat(strwrap(substr(covid_p, 1, 500)), sep="\n")
```

    >WEX55198.1 ORF10 protein [Severe acute respiratory syndrome
    coronavirus 2] MGYINVFAFPFTIYSLLLCRMNSRNYIAQVDVVNFNFT

    >WEX55197.1 nucleocapsid phosphoprotein [Severe acute respiratory
    syndrome coronavirus 2]
    MSDNGPQNQRNALRITFGGPSDSTGSNQNGGARSKQRRPQGLPNNTASWFTALTQHGKEDLKFPRGQGVP
    INTNSSPDDQIGYYRRATRRIRGGDGKMKDLSPRWYFYYLGTGPEAGLPYGANKDGIIWVATEGALNTPK
    DHIGTRNPANNAAIVLQLPQGTTLPKGFYAEGSRGGSQASSRSSSRSRNSSRNSTPGSSKRTSPARMAGN
    GGDAALALLLLDRLNQLESKMSGKGQQQQGQTVTKKSAAEASKKPRQKRTATKAYNVTQAFGRRGPEQTQ
    GNFGDQELIR

And let’s read it as an `AAStringSet`

``` r
temp <- tempfile()
write(covid_p, temp)
covid_p1 <- readAAStringSet(temp)
head(names(covid_p1))
```

    [1] "WEX55198.1 ORF10 protein [Severe acute respiratory syndrome coronavirus 2]"              
    [2] "WEX55197.1 nucleocapsid phosphoprotein [Severe acute respiratory syndrome coronavirus 2]"
    [3] "WEX55196.1 ORF8 protein [Severe acute respiratory syndrome coronavirus 2]"               
    [4] "WEX55195.1 ORF7b [Severe acute respiratory syndrome coronavirus 2]"                      
    [5] "WEX55194.1 ORF7a protein [Severe acute respiratory syndrome coronavirus 2]"              
    [6] "WEX55193.1 ORF6 protein [Severe acute respiratory syndrome coronavirus 2]"               

Following the Example above extract the identifiers example `WEX55198.1`
using a regex ??????

1.  Using `str_extract` and `^` and `.` and `{1,10}`

``` r
str_extract(names(covid_p1), "^.{1,10}")
```

     [1] "WEX55198.1" "WEX55197.1" "WEX55196.1" "WEX55195.1" "WEX55194.1"
     [6] "WEX55193.1" "WEX55192.1" "WEX55191.1" "WEX55190.1" "WEX55189.1"
    [11] "WEX55188.1" "WEX55187.1" "WEX55186.1" "WEX55185.1" "WEX55184.1"
    [16] "WEX55183.1" "WEX55182.1" "WEX55181.1" "WEX55180.1" "WEX55179.1"
    [21] "WEX55178.1" "WEX55177.1" "WEX55176.1" "WEX55175.1" "WEX55174.1"
    [26] "WEX55173.1" "WEX55172.1" "WEX55171.1" "WEX55170.1" "WEX55169.1"

1.  Using `str_extract` and `^\\S*`

``` r
str_extract(names(covid_p1),"^\\S*")
```

     [1] "WEX55198.1" "WEX55197.1" "WEX55196.1" "WEX55195.1" "WEX55194.1"
     [6] "WEX55193.1" "WEX55192.1" "WEX55191.1" "WEX55190.1" "WEX55189.1"
    [11] "WEX55188.1" "WEX55187.1" "WEX55186.1" "WEX55185.1" "WEX55184.1"
    [16] "WEX55183.1" "WEX55182.1" "WEX55181.1" "WEX55180.1" "WEX55179.1"
    [21] "WEX55178.1" "WEX55177.1" "WEX55176.1" "WEX55175.1" "WEX55174.1"
    [26] "WEX55173.1" "WEX55172.1" "WEX55171.1" "WEX55170.1" "WEX55169.1"

1.  Using `str_extract` and `^([\\S]+)`

    ``` r
    str_extract(names(covid_p1),"^([\\S]+)")
    ```

         [1] "WEX55198.1" "WEX55197.1" "WEX55196.1" "WEX55195.1" "WEX55194.1"
         [6] "WEX55193.1" "WEX55192.1" "WEX55191.1" "WEX55190.1" "WEX55189.1"
        [11] "WEX55188.1" "WEX55187.1" "WEX55186.1" "WEX55185.1" "WEX55184.1"
        [16] "WEX55183.1" "WEX55182.1" "WEX55181.1" "WEX55180.1" "WEX55179.1"
        [21] "WEX55178.1" "WEX55177.1" "WEX55176.1" "WEX55175.1" "WEX55174.1"
        [26] "WEX55173.1" "WEX55172.1" "WEX55171.1" "WEX55170.1" "WEX55169.1"

2.  Using `str_extract`and `[:alpha:]` and `[:digit:]` and `[:punct:]`

    ``` r
    str_extract(names(covid_p1), "[:alpha:]{3}+[:digit:]{5}+[:punct:]+[:digit:]")
    ```

         [1] "WEX55198.1" "WEX55197.1" "WEX55196.1" "WEX55195.1" "WEX55194.1"
         [6] "WEX55193.1" "WEX55192.1" "WEX55191.1" "WEX55190.1" "WEX55189.1"
        [11] "WEX55188.1" "WEX55187.1" "WEX55186.1" "WEX55185.1" "WEX55184.1"
        [16] "WEX55183.1" "WEX55182.1" "WEX55181.1" "WEX55180.1" "WEX55179.1"
        [21] "WEX55178.1" "WEX55177.1" "WEX55176.1" "WEX55175.1" "WEX55174.1"
        [26] "WEX55173.1" "WEX55172.1" "WEX55171.1" "WEX55170.1" "WEX55169.1"

3.  Using `sub`

    ``` r
    sub("^.*(WEX\\d{5}\\.\\d{1}).*$", "\\1", names(covid_p1))
    ```

         [1] "WEX55198.1" "WEX55197.1" "WEX55196.1" "WEX55195.1" "WEX55194.1"
         [6] "WEX55193.1" "WEX55192.1" "WEX55191.1" "WEX55190.1" "WEX55189.1"
        [11] "WEX55188.1" "WEX55187.1" "WEX55186.1" "WEX55185.1" "WEX55184.1"
        [16] "WEX55183.1" "WEX55182.1" "WEX55181.1" "WEX55180.1" "WEX55179.1"
        [21] "WEX55178.1" "WEX55177.1" "WEX55176.1" "WEX55175.1" "WEX55174.1"
        [26] "WEX55173.1" "WEX55172.1" "WEX55171.1" "WEX55170.1" "WEX55169.1"

In this regular expression, the **`^.*`** at the beginning matches any
characters at the start of the string, and the **`.*$`** at the end
matches any characters at the end of the string. The **`()`** captures
the ID as a group, and the **`\\1`** in the replacement string inserts
the captured group (i.e., the ID) into the final result.

``` r
str_extract(names(covid_p1), "(WEX\\d{5}\\.\\d{1})")
```

     [1] "WEX55198.1" "WEX55197.1" "WEX55196.1" "WEX55195.1" "WEX55194.1"
     [6] "WEX55193.1" "WEX55192.1" "WEX55191.1" "WEX55190.1" "WEX55189.1"
    [11] "WEX55188.1" "WEX55187.1" "WEX55186.1" "WEX55185.1" "WEX55184.1"
    [16] "WEX55183.1" "WEX55182.1" "WEX55181.1" "WEX55180.1" "WEX55179.1"
    [21] "WEX55178.1" "WEX55177.1" "WEX55176.1" "WEX55175.1" "WEX55174.1"
    [26] "WEX55173.1" "WEX55172.1" "WEX55171.1" "WEX55170.1" "WEX55169.1"

## Available regex generators

https://regex101.com/

https://regex-generator.olafneumann.org/

## References

http://perso.ens-lyon.fr/lise.vaudor/strings-et-expressions-regulieres/

https://debuggingdata.com/post/r/regular-expressions-look-arounds/

https://cran.r-project.org/web/packages/rentrez/vignettes/rentrez_tutorial.html

https://www3.ntu.edu.sg/home/ehchua/programming/howto/Regexe.html
