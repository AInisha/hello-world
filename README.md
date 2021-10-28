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



 Layers:-
 
Input for both encoder decoder is 2 dimesional shape, whereas the output vector is 3 dimensional where each timestep represent num_enocder_token features(vocabulary)
Embedding layer in both decoder and encoder is used for 300 latent dimension giving weights to features also mask_zero is kept True to ignore padding considering some words are missing or not avalaible and should be ignored.
 

For encoder 2 Bidrectional layer is used with 300 output dimension, the second bidirectional lstm is initialized with states of first bilstm layer. (Note in case of bahadanu attention mechanism, where longer sequence prediction is needed we use hidden states of all encoder for creating context vector hence use return_sequence= True in order to return hidden state for each time step during encoding.)
States of last bidrectional will be used discarding encoder output hence forward and backward states are added to create hidden and cell state.
 
The hidden and cell state of encoder will be used as an initial state for decoder, we use only 1 lstm layer and the latent dims remains same where we return output at every timestep store in decoder output. During unrolling, Timedistributed layer apply same dense layer to every timesteps(Using return_sequences=False, the Dense layer will get applied only once in the last cell). This way we achieve 3 dimensional output.
 

 
 
 
Inference Model:-
The trained model cannot be used directly for prediction, reason is correct output should be known beforehand. Therefore, I have provide the predicted output as the input.
Trained layers of encoder and decoder is used creating a seperate encoder model and decoder model. 
I have taken decoder input data such that it begins with a special symbol 'Start_ '. 
Decoder's output at time step t will be used decoder's input at time step t+1, when it generates "_END" symbol  as output, or when it exceeds the number of maximum outputs defined the predicted will be completed.
However, The new model will not use Teacher Learning as we are giving predicted output to decoder.

Overall, Accuracy achieved 97% with 105 epochs, the model was able to achieve good amount of improvement with less dataset giving a higher chance for better Human interaction with larger dataset. 
