#Summary
#Sequence to Sequence Model(Encoder Decoder Approach to achieve the puurpose of Human interactive BOT)

Preprocessing (in seperate file)
In preprocessing.ipynb, I involved myself  in cleaning of unprocessed movies dataset with regular expression and  also taken to take little deeper dive into the process of correcting the spelling using norvig's approach here is the paper  :-https://norvig.com/spell-correct.html in order to understand how spellings are corrected,
4 methods was created in this approach , starting with the 'edit1' function which creates new list of words by approach of removing  character, with and without replacing any letter in given word and adding an extra alphabet to the same, edit2 function is call to edit1 function to create more ambigious words, following which there is a 'known' function which select known words from the list by comparing against corpus and it is called by 'candidate' function forming a new list. This list is passed to 'P' function which choose the word with highest probabality which is called under 'correction' function.
Few drawback observed for the above is it does following error:-
'unknownunkonwn' was return as 'unknownunknown'km hence donot remove reptitive words
for 'offff' it corrects to 'off' 
and only do the correction by replacing incorrect word with highest probabality one, hence the context of complete sentence is completely ignored everytime


I have attached the Uncleaned conversation text file for reference, where i have extracted uninterupted conversation between actors in seperate list and assigned key to each forming a dictionary, and created context (where the person is initiating conversation) and target list(where the person is replying to the context given). in loop for both target and context I call 'clean_text' function to lowercse , removing any punctuation and complete the auxiliary verbs.
I create a vocab dictionary with word count and word including both target and context. also create another dictionery (for word to ID mapping) where I donot add from vocab those words which are not in corpus keep the threshold 1 to not loose much data and manaully add '<UNK>' to the dictionery. 
 
 
  
  
  
 ---------**********----------
 
 
System limitation challenge:-
For the above general idea was to keep some threshold on all vocab before adding to dictionery(it is word to ID mapping dictionery) in order to replace less used and incorrect words. So during prediction those words will be replaced with <UNK> however,
Major challenges faced during training model accuracy improvement was in small amount at each epoch, difference was small amount hence required 150+ epochs with larger dataset, there is constant challenge of colab crashing seen at times lead to removing of <UNK> from context to ID dictionery and using very small dataset with 105 epochs giving 97 accuracy to model. 
 
 
  
 -------*********--------
 

 
 
For the reference my training model and inference model is in 'Chat_Conversation_Usecase1.ipynb' file with accuracy 97% for 27000 dataset.

I have created dataframe with processed movie dataset, involving few more preprocessing step with adding 'START_ ' in prefix and ' _END' in suffix in each sentence for target column useful for Inference model.
 
I created a set of total vocabulary taken from both target, context, with that created input_token_index to map word to index saved as a dictionery but we start with an index 1 we are saving 0 value for padding which will be ignore during training process, creating reverse dictionery to map index to word under reverse_input_char_index.
 
 
 'generatebatch' function is created which takes X, y, batchsize as an input and yield 3 array(encoder_input_data, decoder_input_data, decoder_target_data) of provided batch size at a time. In this function padding and context to ID mapping is done for each word:-
For Encoder and decoder model we have used variable-size input and output hence post 0 padding will be applied to both X, y, in ANN we provide fixed number of nuerons for first and last layer hence input and output shape will be created of similar length.

 
As model is build on the concept of Teacher forcing method to improve the training process, hence 3 array is given -2 input and 1 output.
I am giving parallel dataset (encoder_input_data, decoder_input_data) to the model, so model gets both context and target as input to get optimal weights for prediction.
Decoder produce the output sequence one by one, For each output, the decoder consumes a context vector and an input.
The initial context vector is created by the encoder
The initial input is a special symbol for decoder to make it start, e.g. 'START_ '
Using initial context and initial input, decoder will generate first output
For the next output, decoder will use its current state as context vector and generated (predicted) output as input, but Model (as the teacher!) provide the correct output to the decoder as input.
Hence decoder use the context vector and the correct input to next output rather than using its prediction in previous cycle.
During training:

At the first cycle, decoder will use encoder's state and its first input which is 0 from decoder input sequence [0 8 2 7] to generate the first output which is expected to be 8 from [8 2 7 4]
Assume that decoder predicts 7
In generic Encoder-Decoder model, decoder will use 7 to generate/predict next token
In teacher forcing, we (the teacher!) provide the second input from [0 8 2 7] which is 8 to the decoder to generate/predict next token
Thus, during training, teacher enforces the decoder to condition itself to generate/predict next token according to the given correct input!
 
 
 
 embedding
 
 we need to inform the model that some part of the data is actually padding and should be ignored during processing
 
 
 
In testing
encoder will consume all the sequence and the paddings
decoder will stop predicting
when it generates "_END" symbol  as output, or when it exceeds the number of maximum outputs defined.
 
 
 
 



 
 
 
 
 
 
 









