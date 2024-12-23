# Intent-Classification-Indoml

﻿There are various ways by which we have trained the model, which you can use easily by just doing some changes in the code.
=> while training the model every time make sure to replace the data_path and data_label_path with the path of utterances(surprise.data) file of surprise dataset and with the path of intents(surprise.solutions) file of the surprise dataset respectively.
1. Set-fit training:-  While running the code, make sure to first install the dependencies, then replace put the path of data(data_path) and data_label_path to be the path of your file surprise.data and surprise.solution file and in test_data replace path test_data with the path of massive_test.data.
Then load your pretrained model. You can use both the model as mentioned by me or can also use the pretrained model from the huggingface trained on the same intent classification task but make sure that the number of classes or intent it has been trained on should be atleast 10. After converting data into CSV , load that using datasets and load pretrained model via setFitModel and run the SrtfitTrainer, you can here modify the parameters as per your convenience.


2. 1-stage Training:- 1st do the same install dependencies as mentioned in the file, and also put the path of data correctly. While loading the tokenizer just replace model name with the pretrained model you want to use, Here I have use mainly “ibm/roberta-large-vira-intents”, “vira-chatbot/roberta-large-vira-intents-live”, “habana-xlm-r-large amazon massive” and “roberta-large” , you can also use other models, while loading the tokenizer and model make sure that if the pretrained model is of roberta type and tokenizer should also be of Roberta tokenizer because different-2 tokenizer of different-2 model type tokenizer the words in a different way, and also the sequence classification model should also be of same type.Then while running the training just replace saved dir with the path where you want to save all the model weights, you can choose yourselves that whether you want to save the weights after every step or every epoch or after certain number of steps, I would suggest you to save the weights after every epochs.
If in this 1-stage or in other cases you want to use data augmentation then you can use that also or k-fold cross validation also.
Data Augmentation:- Data augmentation using the nlpaug library. Here we have 2 options available in the data augmentation, we can either take synonym replacement or random insertion, where in the synonym replacement it takes every sentence of the surprise dataset and we replace words in the text with synonyms while maintaining the original context, While in the random insertion we insert new words into our original sentence at random positions. We have to add words in such a way that the context or meaning of the sentence doesn’t change, Here we can take both of them for augmenting our data, or one of them, Here I have taken the synonym replacement with generating 2 more sentences for every sentence, so that new dataset size becomes the 3 times of the surprise dataset . Here user can take other options also where he can also include random insertion in his training dataset and also user can decide that how many sentence he has to generate for every sentence, but I would suggest to not take more that 4 or 5 sentences. While performing data augmentation we are also doing some cleaning of the data like removing extra spaces, double quotes from the generated sentences.


K-fold cross Validation:- 
2-Stage training:- In the case of 2 stage training first do the same things, then what I have done is load the ntu_adl_intent dataset from the hugging face, you can do the same also by using dataset but first make sure to install all the dependencies. Then after performing all kinds of preprocessing on the dataset, just choose the model which you want to fine tune, I have here use the fine tune, and then Just train the model, and saved_dir will be the path where all the weights of the model will get saved. Then the best Weights(checkpoint) will be used for fine tuning the model in 2nd stage on the Surprise Dataset, For Fine tuning on the surprise dataset we will first load the data from json file(we will load surprise.data and surprise.solutions), make sure that path of the files is correct, If you want then you can load the Blind dataset in the same file and also test the model here. After that while initialising the model, use the model name to be the path of the best checkpoint based on the validation score obtained from the first stage. You can also take the other checkpoint based on your convenience. Then initialise the tokenizer and model. After that, train the model on that surprise dataset after performing all kinds of preprocessing on the given dataset and again save the weights(Checkpoints) in any directory by specifying the path of that. You can decide after how many steps or epoch you want to save every model as per convenience. I have saved them after every epoch. Then the best Checkpoint will be used for predicting the labels of the Surprise dataset.
[First run  the file named “indoml_roberta_large_2nd_stage_fine_tuning”, get the best checkpoint, then run the file named, “intent_classificiation_indoml_pretraining_roberta_large_1st_stage” get the best checkpoint and use it to predict the labels of testing data] 
3.Extra layer:-  To run this code we have saved and loaded one extra module “encoder layer” along with the  main module. Rest is similar to 1-stage Training. 


4.Voting Ensemble:-  In the voting ensemble I have done it in 2 ways first where if my number of files increases or decreases then I have to modify the code accordingly.  Here I will discuss the voting og n number of files where n can be any integer greater than 2 (n>=2), where I have to just provide the value of n and in the file_paths give the path of massive_test.predict files which contain 6000 entries where each entry is in the form of dictionary, containing indoml_id and corresponding intent predicted by the model as items and after that take the mode along axis=1 for all entries, if mode remains same for some of entries then the label for that entry will be label of the file having best score individually, which in our this case is intent_1, you can modify this as per your convenience.
Testing on the Blind dataset:- 
I have provided a separate test.ipynb file for testing the model but you can also do the same step after training the model and saving its weights. In the same file where you are training the model. First you need to install the dependencies, then import the necessary modules. Then I will discuss inside the function what I have done. First you need to ensure that you have data_path, which is a directory containing all 3 files of the surprise dataset and also the massive dataset. Then just read json data from those files, then you need to extract the unique labels from the json data read from the surprise.solutions file which will be used to make the label 2id and id2 label mapping. Note that label2id mapping and id2label mapping you make should be the same for both while training the model as well as testing the model.
For example, if the label accept reservation and meal suggestions have id 5 and 40 while training the model then at the times of testing the same id should be there for both the labels.
Here in the testing I have made both the mapping same code will be used for forming the mapping at the time of training the model. After that we just load the pretrained model and the tokenizer with the model path being the best checkpoint(weights) saved after training the model on the  surprise dataset. Then  we just find the predictions but iterate over the testing data loader and after all such steps convert the predictions into the desired formatThis output.predict file will got saved in the same directory where this jupyter file is present, you can also change the path of this where you want to save it accordingly. 
Another thing i want to say that you can also make your own label2id and id2label mapping at a separate place and just put them here in dictionary format and in  that case you data_path will be path of massive testing data, you don’t need to give any directory or folder path, just give the path of the testing data containing 6000 samples, rest all things remains same.
Here for testing the model on the checkpoints obtained from all the training files except extra layer use test.ipynb and for the extra layer indoml_testing_extra_layer.ipynb file
