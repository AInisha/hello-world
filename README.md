# hello-world
Sequence to Sequence Model(Encoder Decoder Approach to achieve the puurpose of Human interactive BOT)

Preprocessing (in seperate file)
In preprocessing.ipynb, I involved myself  in cleaning of unprocessed movies dataset with regular expression and  also taken to take little deeper dive into the process of correcting the spelling using norvig's approach here is the paper  :-https://norvig.com/spell-correct.html in order to understand how spellings are corrected,
4 methods was created in this approach , starting with the 'edit1' function which creates new list of words by approach of removing  character, with and without replacing any letter in given word and adding an extra alphabet to the same, edit2 function is call to edit1 function to create more ambigious words, following which there is a 'known' function which select known words from the list by comparing against corpus and it is called by 'candidate' function forming a new list. This list is passed to 'P' function which choose the word with highest probabality which is called under 'correction' function.
Few drawback observed for the above is it does following error:-
'unknownunkonwn' was return as 'unknownunknown'km hence donot remove reptitive words
for 'offff' it corrects to 'off' 
and only do the correction by replacing incorrect word with highest probabality one, hence the context of complete sentence is completely ignored everytime


I have attached the Uncleaned conversation text file for reference, where i have extracted uninterupted conversation between actors in seperate list and assigned key to each forming a dictionary, and created context (where the person is initiating conversation) and target list(where the person is replying to the context given). in loop for both target and context I call 'clean_text' function to lowercse , removing any punctuation and complete the auxiliary verbs.
I create a vocab dictionary with word count and word including both target and context. also create another dictionery (for context to ID mapping) where I donot add from vocab those words which are not in corpus keep the threshold 1 to not loose much data and manaully add '<UNK>' to the dictionery. 
 
 
  
  
  
  ---------**********----------
 
 
System limitation challenge:-
For the above general idea was to keep some threshold on all vocab before adding to dictionery(it is context to ID mapping dictionery) in order to replace less used and incorrect words. So during prediction those words will be replaced with <UNK> however,
Major challenges faced during training model accuracy improvement was in small amount at each epoch, difference was small amount hence required 150+ epochs with larger dataset, there is constant challenge of colab crashing seen at times lead to removing of <UNK> from context to ID dictionery and using very small dataset with 105 epochs giving 97 accuracy to model. 
  
 -------*********--------
 
 
For the reference my training model and inference model with results is in 'Chat_Conversation_Usecase1.ipynb' file with accuracy 97% for 27000 dataset.
I have created dataframe with processed movie dataset, involving few of our preprocessing step including adding 'START_ ' in prefix and 'END_' in suffix of every sentence in target column for inference
 
create total vocabulary under set and created input_token_index that is context to index for those vocabulary under a dictionery but we start index 1 as  we are saving 0 for padding which will be ignore during training process, creating reverse dictionery from index to word reverse_input_char_index.
 
'generatebatch' function is created which takes X, y, batchsize and yield 3 array with batch size at a time. In this function padding and context to ID mapping is done for each word.
 
 
 
 
 












