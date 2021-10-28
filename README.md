# hello-world
Sequence to Sequence Model(Encoder Decoder Approach to achieve the puurpose of Human interactive BOT)

Preprocessing 
In preprocessing.ipynb, I involved myself  in cleaning of unprocessed movies dataset with regular expression and  also taken to take little deeper dive into the process of correcting the spelling using norvig's approach here is the paper  :-https://norvig.com/spell-correct.html in order to understand how spellings are corrected,
4 methods was created in this approach , starting with the 'edit1' function which creates new list of words by approach of removing  character, with and without replacing any letter in given word and adding an extra alphabet to the same, edit2 function is call to edit1 function to create more ambigious words, following which there is a 'known' function which select known words from the list by comparing against corpus and it is called by 'candidate' function forming a new list. This list is passed to 'P' function which choose the word with highest probabality which is called under 'correction' function.
Few drawback observed for the above is it does following error:-
'unknownunkonwn' was return as 'unknownunknown'km hence donot remove reptitive words
for 'offff' it corrects to 'off' 
and only do the correction by replacing incorrect word with highest probabality one, hence the context of complete sentence is completely ignored everytime














There 

With the clean Movie Dataset

