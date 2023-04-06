Regular Expression in R
================
Hedia Tnani

Regular Expressions

What are Regular Expressions?

Regular expressions (regex or regexp) are patterns used to match and manipulate text data. They are widely used in text processing and are supported by many programming languages, including R.

Regex patterns consist of a combination of letters, digits, and special characters that define the search criteria. The basic idea is to describe a set of strings using a concise and flexible syntax. For example, you could use a regex pattern to search for all email addresses in a text file or to extract all dates from a web page.

Regular expressions are extremly useful to help clean data and parse strings. They are very helpful in bioinformatics. 

Playing with regex

The grep Function

The grep function in base R is used to search for patterns in a character vector and returns the indices of the elements that contain the pattern. 

{r}
# create a character vector
words <- c("bioinformatics", "mathematics", "biology", "chemistry", "computer science")

# search for all elements that contain the letter "a"
grep("a", words)


In this example, the grep function searches for all elements in the words vector that contain the letter "a" and returns the indices of the matching elements.

sub and gsub Functions

The sub and gsub functions are used to replace a pattern in a string with another pattern. The sub function replaces the first occurrence of the pattern, while the gsub function replaces all occurrences of the pattern. 

{r}
# create a character vector
text <- "Mathematics is very difficult sometimes. Mathematics is not easy for biologists to understand"

# replace the word "Mathematics" with "Bioinformatics"
sub("Mathematics", "Bioinformatics", text)

# replace all occurrences of the word "is" with "IS"
gsub("is", "IS", text)

In the example above, the sub function replaces the first occurrence of the word "Mathematics" with "Bioinformatics" in the text vector. In the second example, the gsub function replaces all occurrences of the word "is" with "IS" in the text vector.

{r}
# search for all elements that contain "Mathematics" or "biologISts"
grep("(Mathematics|biologISts)", text)

The str_subset function

str_subset() from the stringr package returns all elements of string where there's at least one match to pattern. It's a wrapper around x[str_detect(x, pattern)], and is equivalent to grep(pattern, x, value = TRUE). 

{r}
library(stringr)
str_subset(text, "(Mathematics|biologISts)")

Let's get the words starting with b from the previous words object.  "^b" is a regular expression pattern that matches any string that starts with "b".

{r}
str_subset(words, "^b")

When you call str_subset(words, "^b"), the function searches each string in words for the pattern "^b", and returns only the strings that start with "b". The result is a new character vector that includes only the matching strings.

Let's get the words ending with s from the previous words object. "s$" is a regular expression pattern that matches any string that ends with "s". 

{r}
str_subset(words, "s$")

When you call str_subset(words, "s$"), the function searches each string in words for the pattern "s$", and returns only the strings that end with "s". The result is a new character vector that includes only the matching strings.

Let's get the words that matches either "c" or "s". 

{r}
str_subset(words, "[cs]")

str_subset(words, "[cs]") will return all strings in words that contain either "c" or "s".

The gregexpr() function

gregexpr() is a base R function that searches for matches of a regular expression in a character string and returns the position of the matches. It can be used to find all occurrences of a pattern in a string, even if there are multiple matches.

{r, message=FALSE}
library(Biostrings)

# Create a DNAStringSet object
dna_strings <- DNAStringSet(c("ACGTAGTTCAAGTACAGATCGGGGGCTAGCCCCCTAGCTAGCT", "ACGTATTTTGTACAGATAAAACGCTAGCTAGCTAGCTAGATCGGGGGC"))

# Set names for the sequences
names(dna_strings) <- c("Sequence1", "Sequence2")

dna_strings

 gregexpr finds all matches for the regular expression "TA" in each of the sequences. 

{r}
# Use gregexpr to find matches for a regular expression in the DNAStringSet
matches <- gregexpr("TA", dna_strings)
matches

The output of gregexpr is a list where each element corresponds to one of the input sequences. For each sequence, gregexpr returns a vector of the positions where matches were found, along with the length of each match. If no matches were found for a sequence, gregexpr returns an empty integer vector.

{r}
# Find the position of the pattern "GGGGG" using base R
unlist(gregexpr("G{5}", dna_strings))

You can use the regmatches() function along with gregexpr() . 

{r}
# Use gregexpr and regmatches to extract matched sequences
matched_seqs <- regmatches(dna_strings, gregexpr("G{5}", dna_strings))

# View the matched sequences
matched_seqs

The gregexpr() function is used to identify the positions of the matches, and regmatches() is used to extract the matched sequences based on those positions. The resulting object is a list where each element corresponds to the matched sequence(s) for a sequence in the DNAStringSet.

Use case: SCHIZOPHRENIA

In this tutorial, we will explore how to use regular expressions in R using base R and tidyverse packages to analyze data from the rentrez package on schizophrenia. 

Getting the Data

{r}
# install and load rentrez package
#install.packages("rentrez")

# load the library
library(rentrez)

# search for articles related to schizophrenia
schizo_search <- entrez_search(db="pubmed", term="schizophrenia")

# get the ids for the top 20 articles
schizo_ids <- entrez_fetch(db="pubmed", id=schizo_search$ids[1:20], rettype="xml", parsed = T)
class(schizo_ids)

{r}
schizo_list <- XML::xmlToList(schizo_ids)
#schizo_list

{r}
schizo_list[[1]]$MedlineCitation$Article$ArticleTitle

schizo_list is a list of nested objects containing MedlineCitation information, you can use map from the purrr package to extract all the ArticleTitles from each nested list.

{r}
library(purrr)
# Use map with .x to extract the ArticleTitle from each nested list
titles <- map(schizo_list, ~ .x$MedlineCitation$Article$ArticleTitle)
titles

Using regular expression

{r}
# find the indices of titles that contain the word "schizophrenia"
grep("schizophrenia", titles)

# find the indices of titles that contain the words "schizophrenia" or "psychosis"
grep("schizophrenia|psychosis", titles)

Let's subset the titles object:

{r}
# find the titles that contain the word "schizophrenia"
titles[grep("schizophrenia", titles)]

{r}
# find the titles that contain the words "schizophrenia" or "psychosis"
titles[grep("schizophrenia|psychosis", titles)]

{r}
# Search for articles on "schizophrenia" that mention "antipsychotic" in the title
search <- "schizophrenia AND antipsychotic[TIAB]"
abstracts <- entrez_search(db = "pubmed", term = search, retmax = 10)$ids
schizoAntipsy <- entrez_fetch(db = "pubmed", id = abstracts, rettype = "xml", parsed = T) 
schizoAntipsy_list <- XML::xmlToList(schizoAntipsy)
titles <- map(schizoAntipsy_list, ~ .x$MedlineCitation$Article$ArticleTitle)
titles

{r}
# replace the word "schizophrenia" with "SZ" in each title
str_replace(titles, "(s|S)chizophrenia", "SZ")

In this example, we use the str_replace function from stringr to replace the word "schizophrenia" or "Schizophrenia" with "SZ" in each title in the titles vector.

The str_extract Function

str_extract() function extracts substrings that match the regular expression pattern.

{r}
# extract the first word of each title
str_extract(titles, "\\w+")

When typing regular expressions, there are a group of special characters called metacharacters that have other functions. These are .{()\^$|?*+ .

The backslash is SUPER important because if we want to search for any of these characters without using their built in function we must escape the character with a backslash. 

The backslash is a metacharacter too! So to create a backslash for the function to search with, we need to escape the backslash!

Let´s extract the first digit (0-9) that appears in each element of the titles character vector.

{r}
# extract the first digit of each title
str_extract(titles, "[:digit:]{1}")

The code str_extract(titles, "[:digit:]{1}") extracts the first digit (0-9) that appears in each element of the titles character vector using the str_extract() function from the stringr package.

The regular expression [:digit:]{1} matches a single digit character. The curly braces {1} indicate that we want to match exactly one digit character, and the square brackets [] are used to define a character class. The [:digit:] character class matches any digit character.

Thus, str_extract(titles, "[:digit:]{1}") will return a vector of the first digit character found in each element of the titles vector. If an element does not contain any digit characters, the result will be an empty string for that element.

Use case: Covid 19 and SARS-CoV-2

In January 2020, scientists deciphered the genome of the SARS-CoV-2 virus, which causes Covid-19. The sample came from a 41-year-old man who worked at the Wuhan seafood market, where the first group of cases appeared.

Researchers are now striving to make sense of this viral recipe, which could inspire drugs, vaccines, and other tools to fight the current pandemic.

As bioinformaticians, we want to study the different genomes and proteomes of SARS-CoV-2, which causes Covid-19.

SARS-CoV-2
The genome of the new coronavirus is a sequence of 30,000 base pairs. (The human genome has more than 3 billion). Scientists have identified the genes for no less than 29 proteins, which perform a whole range of tasks from making copies of the coronavirus to suppressing the body's immune responses.




Source


With the availability of the COVID-19 genome publicly available on the National Center for Biotechnology Information (NCBI) website, we are interested in exploring the genome of SARS-CoV-2 in the first place.
Let's start by installing the library we will use to search the NCBI database.

Getting the data

First, you can use entrez_dbs() to find the list of available databases:

{r}
library(rentrez)
entrez_dbs()

There is a set of functions with names starting entrez_db_ that can be used to gather more information about each of these databases:

Functions that help you learn about NCBI databases

Function name

Return

entrez_db_summary()

Brief description of what the database is

entrez_db_searchable()

Set of search terms that can used with this database

entrez_db_links()

Set of databases that might contain linked records

As a bioinformatician, we want to find the first 150 records in the nucleotide database that match Severe acute respiratory syndrome coronavirus 2. How many records in the nucleotide sequence database match Severe acute respiratory syndrome coronavirus 2?

{r}
covid <- "Severe acute respiratory syndrome coronavirus 2"
covid_nuc <- entrez_search(db="nuccore", term=covid,retmax = 30)
covid_nuc$count

What's the structure of the covid_nuc object?

{r}
str(covid_nuc)

How to get the FASTA sequence of the nucleotide sequences of the above covid_nuc$ids ?

By providing a vector of IDs in covid_nuc$ids, the entrez_fetch() function retrieves the corresponding nucleotide sequences in FASTA format from the NCBI Nucleotide database.

{r}
covid_n <- entrez_fetch(db="nuccore", id=covid_nuc$ids, rettype="fasta")
nchar(covid_n)

Congratulations, now you have a really huge character vector! Rather than printing all those thousands of bases we can take a peak at the top of the file:

{r}
cat(strwrap(substr(covid_n, 1, 500)), sep="\n")

If we wanted to use these sequences in some other application we could write them to file:

{r}
write(covid_n, "covid_nuc.fasta")

Alternatively, if you want to use them within an R sessionwe could write them to a temporary file then read that. 

{r}
temp <- tempfile()
write(covid_n, temp)
covid_n1 <- readDNAStringSet(temp)
head(names(covid_n1))

Hands -on Exercice For NUcleotide Sequences

We want to get the different identifiers that are before the \\s and the word Severe. Which regular expression should we use?

Using str_extract  and ^ and . and {1,10}

{r}
str_extract(names(covid_n1), "^.{1,10}")

 The ^ anchor specifies the start of the string. The . is a wildcard character that matches any character. The {1,10} quantifier specifies that the previous element (i.e., the .) can occur between 1 and 10 times, inclusive. In other words, this regular expression will match any string that is between 1 and 10 characters long.

Using str_extract 

 and [:alpha:] and [:digit:] and [:punct:]

{r}
str_extract(names(covid_n1), "[:alpha:]{2}+[:digit:]{6}+[:punct:]+[:digit:]")

This regular expression can be broken down as follows:

[:alpha:]{2}: Matches any two alphabetic characters.

+: The + sign means that the preceding pattern should be matched one or more times. In this case, it applies to the [:alpha:]{2} pattern.

[:digit:]{6}: Matches any six numeric characters.

+: Similar to the previous use, the + sign applies to the [:digit:]{6} pattern.

[:punct:]+: Matches one or more punctuation characters.

[:digit:]: Matches a single numeric character.

Overall, this regular expression matches a string that contains two alphabetic characters, followed by six numeric characters, followed by one or more punctuation characters, and finally a single numeric character.

Using the Positive Lookahead

Lookarounds are helpful to reduce complicated regex expressions and make it easier to identify what we want to find in a string.

There are two types of lookarounds: lookaheads and lookbehinds. In this solution we'll be using the Positive Lookahead (?=pattern)

{r}
str_extract(covid_n, ".*(?=\\sSevere)")

This regular expression consists of two parts:

.*: a greedy quantifier that matches any character (except for newline characters) zero or more times, as many times as possible.

(?=\\sSevere): a positive lookahead that matches a space character followed by the word "Severe", but does not include it in the match.

Together, this regular expression matches any characters (except for newlines) that occur immediately before the phrase "Severe" preceded by a space character. The positive lookahead (?=\\sSevere) ensures that the match ends right before the space character that precedes "Severe", so that "Severe" itself is not included in the match.

Hands -on Exercice For Amino-Acid Sequences

Let's get the data

{r}
covid_prot <- entrez_search(db="protein", term=covid,retmax = 30)
covid_p <- entrez_fetch(db="protein", id=covid_prot$ids, rettype="fasta")
nchar(covid_p)

Same as before

{r}
cat(strwrap(substr(covid_p, 1, 500)), sep="\n")

And let's read it as an AAStringSet

{r}
temp <- tempfile()
write(covid_p, temp)
covid_p1 <- readAAStringSet(temp)
head(names(covid_p1))

Following the Example above extract the identifiers example WEX55198.1using a regex ??????

Available regex generators

https://regex101.com/

https://regex-generator.olafneumann.org/

References

http://perso.ens-lyon.fr/lise.vaudor/strings-et-expressions-regulieres/

https://debuggingdata.com/post/r/regular-expressions-look-arounds/

https://cran.r-project.org/web/packages/rentrez/vignettes/rentrez_tutorial.html

https://www3.ntu.edu.sg/home/ehchua/programming/howto/Regexe.html
