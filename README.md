#Summary


#Sequence to Sequence Model(Encoder Decoder Approach to achieve the puurpose of Human interactive BOT)


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
 

For encoder 2 Bidrectional layer is used with 300 output dimension, the second bidirectional lstm is initialized with states of first bilstm layer. 
States of last bidrectional will be used discarding encoder output hence forward and backward states are added to create hidden and cell state.
 
The hidden and cell state of encoder will be used as an initial state for decoder, we use only 1 lstm layer and the latent dims remains same where we return output at every timestep store in decoder output. During unrolling, Timedistributed layer apply same dense layer to every timesteps(Using return_sequences=False, the Dense layer will get applied only once in the last cell). This way we achieve 3 dimensional output.
 

 
 
 
Inference Model:-
The trained model cannot be used directly for prediction, reason is correct output should be known beforehand. Therefore, I have provide the predicted output as the input.
Trained layers of encoder and decoder is used creating a seperate encoder model and decoder model. 
I have taken decoder input data such that it begins with a special symbol 'Start_ '. 
Decoder's output at time step t will be used decoder's input at time step t+1, when it generates "_END" symbol  as output, or when it exceeds the number of maximum outputs defined the predicted will be completed.
However, The new model will not use Teacher Learning as we are giving predicted output to decoder.
 
Accuracy achieved 97% with 105 epochs, alltogether the model has achieved good amount of improvement with less dataset giving a higher chance for better Human interaction if larger dataset is provided.

