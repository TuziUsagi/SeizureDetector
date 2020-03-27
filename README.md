# SeizureDetector
CNN supported seizure detector for single channel EEG recordings. The model was trained with mouse intrahippocampal EEG dataset generated in the lab.


# Input data
The input data should be your EEG recording data organized in .gzip format.
The .gzip data used in our study were converted from .mat data generated with an existing EEG recording software (Krook-Magnuson, E.,et al., 2013. On-demand optogenetic control of spontaneous seizures in temporal lobe epilepsy. Nature communications, 4(1), pp.1-8.)
The .gzip file contains a dataframe, the following keys are needed in the dataframe:
1. fs - sampling frequency
2. recStart - recording start time
3. numOfChannels - number of channels recorded in the file
4. signalLength - the number of datapoint in this recording
5. signal - the EEG signal, organized in the 2D array, a column contains the signal for a channel.

# Model serving for seizure detection
Run the analysisAll.py to detect seizure in your EEG recordings. 
Specify your EEG input data as the inBaseDir, and the output data as the outBaseDir.
The output format will be organized as a dataframe as follows:
1. ['rawPrediction'] - predicted probability of all EEG signal segment (1024 EEG signal point/prediction window)
2. ['seizure_50'], ['seizure_55'], ['seizure_65'], ['seizure_75'], ['seizure_85'], ['seizure_95'], ['seizure_99'] - EEG signal segments were examined and combined when necessary. If two prediction window both contains seizure spikes and are less than 2s away, they are combined. The seizure_number shows the prediction threshould used in the analysis. Note that the minimal seizure length has not yet been setted in here.
3. ['numOfSeizure_50'], ['numOfSeizure_55'], ['numOfSeizure_65'], ['numOfSeizure_75'], ['numOfSeizure_85'], ['numOfSeizure_95'], ['numOfSeizure_99'] - number of seizure detected in the recording, with different prediction threshoulds. Note that the minimal seizure length has not yet been setted in here.
4. ['seizure_2steps'], ['seizure_4steps'], ['seizure_6steps'], ['seizure_8steps'], ['seizure_10steps'] - seizure start and end time for all predicted seizure. Seizures were detected with minimal prediction window steps. Each step is 3 seconds, so the seizure_2steps means  6s for the minimal seizure duration.
5. ['numOfSeizure_2steps'], ['numOfSeizure_4steps'], ['numOfSeizure_6steps'], ['numOfSeizure_8steps'], ['numOfSeizure_10steps'] - number of seizures after setting the minimal seizure duration.

