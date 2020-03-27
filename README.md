# SeizureDetector
CNN supported seizure detector for single channel EEG recordings. The model was trained with mouse intrahippocampal EEG dataset generated in the lab.

A convolutional neural network (CNN) machine learning architecture was designed in TensorFlow to take the raw EEG signal (250 Hz) as the input (height x width = 1 x 1024). After the input layer, the CNN consisted of three convolution-pooling units, a flattened layer, a dropout layer, a dense layer, and a final output layer. Each convolution-pooling unit consisted of a convolution layer and a max pooling layer. The 1st, 3rd and 5th layers were convolution layers, which computed the signal with kernel size x num of kernel as 1x64x16, 1x32x32, and 1x16x8, respectively. The 2nd, 4th, and 6th layers were max pooling layers with 1x2 sub-sampling. The signals were then flattened (7th layer) and connected to a dropout layer (8th) for regularization. The 9th layer was a dense layer connected to the final output layer by a sigmoid activation function. 

Important note: dilated convolution filters were implemented in the code as part of the eeg_inception_unit. Enable it will provide diluated convolution designed to look at the signal at large scope without neceessary depend the network. Please feel free to enable/disable it in the model.

Extracted EEG traces that contained epileptiform events (high amplitude seizure spikes occurring at 2 Hz frequency and higher, with at least 8 spikes occurring within 4 seconds) were used as positive data in training the CNN. Non-epileptiform events and other randomly extracted stable baseline traces were used as negative data. All data in the dataset were set to be 1024 sampling points. If the EEG trace contained an epileptiform event extending beyond 1024 points, a random portion of 1024 continuous points within the event was selected. The amplitudes of all traces were normalized to a -1 to +1 range centered on the baseline of the trace. The dataset was randomly split into training data (37264 events) and evaluation data (9498 events).  The CNN model achieved 92% recall, 93% specificity, 93% precision, and 93% accuracy with a balanced evaluation dataset (positive:negative = 1:1). On an imbalanced evaluation dataset (positive:negative = 5:1), the model achieved 91% recall, 93% specificity, 73% precision, and 92% accuracy.



# Input data
The input data should be your EEG recording data organized in .gzip format.
The .gzip data used in our study were converted from .mat data generated with an existing EEG recording software (Krook-Magnuson, E.,et al., 2013. On-demand optogenetic control of spontaneous seizures in temporal lobe epilepsy. Nature communications, 4(1), pp.1-8.)
The .gzip file contains a dataframe, the following keys are needed in the dataframe:
- fs - sampling frequency
- recStart - recording start time
- numOfChannels - number of channels recorded in the file
- signalLength - the number of datapoint in this recording
- signal - the EEG signal, organized in the 2D array, a column contains the signal for a channel.

# Model serving for seizure detection
Run the analysisAll.py to detect seizure in your EEG recordings. 
Specify your EEG input data as the inBaseDir, and the output data as the outBaseDir.
The CNN model were imported through getModel.
The getModel.py loads CNN model from the myModel.py.

# Output file format
The output file contains predicted seizures start and end time. Each input file will have its own output file.
The output format will be organized as a dataframe as follows:
- ['rawPrediction'] - predicted probability of all EEG signal segment (1024 EEG signal point/prediction window)
- ['seizure_50'], ['seizure_55'], ['seizure_65'], ['seizure_75'], ['seizure_85'], ['seizure_95'], ['seizure_99'] - EEG signal segments were examined and combined when necessary. If two prediction window both contains seizure spikes and are less than 2s away, they are combined. The seizure_number shows the prediction threshould used in the analysis. Note that the minimal seizure length has not yet been setted in here.
- ['numOfSeizure_50'], ['numOfSeizure_55'], ['numOfSeizure_65'], ['numOfSeizure_75'], ['numOfSeizure_85'], ['numOfSeizure_95'], ['numOfSeizure_99'] - number of seizure detected in the recording, with different prediction threshoulds. Note that the minimal seizure length has not yet been setted in here.
- ['seizure_2steps'], ['seizure_4steps'], ['seizure_6steps'], ['seizure_8steps'], ['seizure_10steps'] - seizure start and end time for all predicted seizure. Seizures were detected with minimal prediction window steps. Each step is 3 seconds, so the seizure_2steps means  6s for the minimal seizure duration. 50% prediction threshold were used. 
- ['numOfSeizure_2steps'], ['numOfSeizure_4steps'], ['numOfSeizure_6steps'], ['numOfSeizure_8steps'], ['numOfSeizure_10steps'] - number of seizures after setting the minimal seizure duration. 50% prediction threshold were used. 

