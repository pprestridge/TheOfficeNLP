# NLP The Office

Training an NLP model on transcripts of episodes of the office to classify the speaker as either Jim or Dwight. Then, a second NLP model will be used to generate lines for Michael

Steps:

    - WebScrape all episodes of the office
    
    - Use TensorFlow to transform lines into padded sequences amnenable for a Nueral Net
    
    - Train a Classification model to determine the speaker based on a line
    
    - Transform all of Michael's lines for a speech generation model
    
    - Train a prediction model to generate Michael's lines
    
## Scrape Data

In order to train an NLP model, we first need to develop our corpus of text. For that, we will parse through scripts looking for lines from Jim and Dwight. The individual office transcripts are listed across 7 different webapages. The first function urlFinder will be used to generate a url for each individual script from the 7 webpages. The second function buildCorpus will actually build the corpus of text for training.

## Preprocessing Inputs

Before training, the sentences/lines needs to be processed before being fed to the NLP model. We will use the Tokenizer and pad_sequences from tensorflow to create tokenized sequences of equal lenghts. The speaker label will also be tokenized.

## Data Cleaning

A lot of lines tend to be on the order of 5 or so words, which is challenging for a classifier model to handle. Although there are dead-giveaway words like when Jim calls Dwight by his name or vice-versa, 5 words isn't enough information to accuratley classify a character. shortSentenceRemover will handle removing all sentences that are less than a specified length. Although this greatly reduces the training data, the quality greatly increases.

## Define Model

For our classifier, we will use a relatively simple NLP model. I wanted to use the Standford Glove embedding weights, but the webiste wouldn't respond when trying to download the weights. Instead, the embedding layer will train itself. Followed by the embedding layer is a Bidirectional LSTM to handle the transfer of meaning forward and backwards in the sentence. Lastly, two dense layers connect to the output. I have the code written such that the model can operate as a binary and multiclass classifier. If the cell containing the characters list is updated to include more characters, the notebook and model will update the preprocessing and output layer of the model.

# Speech Generation

## Input Data Structuring

To train this model. All of Michael's lines need to be broken down to include the preceding words as inputs and next word as the label. For example, let's take his infamous line: “If I had a gun with two bullets and I was in a room with Hitler, Bin Laden, and Toby, I would shoot Toby twice.”

This sentence could then be broken up into a number of training examples:

        Input: "If"          Output = "I"
        
        Input: "If I"        Output = "had"
        
        Input: "If I had"    Output = "a"
        
        Input: "If I had a"  Output = "gun"

        etc.

## Line Generation

With a NLP model that generates the next word in a sequence, we can start a line and let the model finish it. Iteratively, we can recall the output of the model to generate full lines of speech with only an initial seed to prompt the model.
