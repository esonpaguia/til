---
title: Text mining and word cloud fundamentals
tags: [R]
description: How to generate word clouds using R
---

Text mining methods allow us to highlight the most frequently used keywords in a paragraph of texts. One can create a word cloud, also referred as text cloud or tag cloud, which is a visual representation of text data.

The procedure of creating word clouds is very simple in R if you know the different steps to execute. The text mining package (tm) and the word cloud generator package (wordcloud) are available in R for helping us to analyze texts and to quickly visualize the keywords as a word cloud.

# 3 reasons you should use word clouds to present your text data
- Word clouds add simplicity and clarity. The most used keywords stand out better in a word cloud
- Word clouds are a potent communication tool. They are easy to understand, to be shared and are impactful
- Word clouds are visually engaging than a table data

# Steps to create a word cloud in R
1. Create a text file
  - Copy and paste the text in a plain text file (e.g. genesis.txt)
  - Save the file
2. Install and load required libraries

    ```
    # Install
    install.packages("tm")  # for text mining
    install.packages("SnowballC") # for text stemming
    install.packages("wordcloud") # word-cloud generator
    install.packages("RColorBrewer") # color palettes
    install.packages("rstudioapi") # rstudio environment reference

    # Load
    library("tm")
    library("SnowballC")
    library("wordcloud")
    library("RColorBrewer")
    library("rstudioapi")
    ```

3. Text mining
  - Load text

      The text is loaded using Corpus() function from text mining (tm) package. Corpus is a list of a document (in our case, we only have one document).

      1. We start by importing the text file created in Step 1

        To import the file saved locally in your computer, type the following R code. You will be asked to choose the text file interactively.

          ```
          setwd(dirname(rstudioapi::getActiveDocumentContext()$path))
          filePath <- paste(getwd(), "/data", sep = "")
          files <- as.character((list.files(path = filePath)))
          data <- unname(sapply(paste(filePath,.Platform$file.sep,files,sep=""), readLines))
          ```

      2. Load the data as a corpus

          ```
          dataCorpus <- Corpus(VectorSource(data))
          ```

      3. Inspect the content of the document

          ```
          inspect(dataCorpus)
          ```

  - Text transformation

      Transformation is performed using tm_map function to replace, for example, special characters from the text.

      ```
      toSpace <- content_transformer(function (x , pattern ) gsub(pattern, " ", x))
      dataCorpus1 <- tm_map(dataCorpus, toSpace, "/")
      dataCorpus2 <- tm_map(dataCorpus1, toSpace, "@")
      dataCorpus3 <- tm_map(dataCorpus2, toSpace, "\\|")
      ```

  - Cleaning the text

      The tm_map() function is used to remove unnecessary white space, to convert the text to lower case, to remove common stopwords like "the", "we".

      The information value of "stopwords" is near zero due to the fact that they are so common in a language. Removing this kind of words is useful before further analyses. For "stopwords", supported languages are Danish, Dutch, English, Finnish, French, German, Hungarian, Italian, Norwegian, Portuguese, Russian, Spanish and Swedish. Language names are case sensitive.

      You could also remove numbers and punctuation with removeNumbers and removePunctuation arguments.

      Another important preprocessing step is to make a text stemming which reduces words to their root form. In other words, this process removes suffixes from words to make it simple and to get the common origin. For example, a stemming process reduces the words "moving", "moved" and "movement" to the root word, "move".

      The R code below can be used to clean your text:

      ```
      # Convert the text to lower case
      dataCorpus4 <- tm_map(dataCorpus3, content_transformer(tolower))

      # Remove numbers
      dataCorpus5 <- tm_map(dataCorpus4, removeNumbers)

      # Remove english common stopwords
      dataCorpus6 <- tm_map(dataCorpus5, removeWords, stopwords("english"))

      # Remove your own stop word
      # specify your stopwords as a character vector
      dataCorpus7 <- tm_map(dataCorpus6, removeWords, c("said", "will"))

      # Remove punctuations
      dataCorpus8 <- tm_map(dataCorpus7, removePunctuation)

      # Eliminate extra white spaces
      dataCorpus9 <- tm_map(dataCorpus8, stripWhitespace)

      # Text stemming
      #dataCorpus10 <- tm_map(dataCorpus9, stemDocument)
      ```

4. Build a term-document matrix 123

    Document matrix is a table containing the frequency of the words. Column names are words and row names are documents. The function TermDocumentMatrix() from text mining package can be used as follow:

    ```
    dtm <- TermDocumentMatrix(dataCorpus10)
    m <- as.matrix(dtm)
    v <- sort(rowSums(m),decreasing=TRUE)
    d <- data.frame(word = names(v),freq=v)
    head(d, 10)
    ```

5. Generate the Word cloud

    ```
    set.seed(1234)
    wordcloud(words = d$word, freq = d$freq, min.freq = 1,
          max.words=200, random.order=FALSE, rot.per=0.35,
          colors=brewer.pal(8, "Dark2"))
    ```

You can clone this repository to get the finished product.

[biblewordcount](https://github.com/esonpaguia/biblewordcloud)
