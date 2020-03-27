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
['rawPrediction'] = predicts
['seizure_50'] = combineInterval(predicts, 0.50)
['seizure_55'] = combineInterval(predicts, 0.55)
['seizure_65'] = combineInterval(predicts, 0.65)
['seizure_75'] = combineInterval(predicts, 0.75)
['seizure_85'] = combineInterval(predicts, 0.85)
['seizure_95'] = combineInterval(predicts, 0.95)
['seizure_99'] = combineInterval(predicts, 0.99)
['numOfSeizure_50'] = len(ResultDict['seizure_50'])
['numOfSeizure_55'] = len(ResultDict['seizure_55'])
['numOfSeizure_65'] = len(ResultDict['seizure_65'])
['numOfSeizure_75'] = len(ResultDict['seizure_75'])
['numOfSeizure_85'] = len(ResultDict['seizure_85'])
['numOfSeizure_95'] = len(ResultDict['seizure_95'])
['numOfSeizure_99'] = len(ResultDict['seizure_99'])
['seizure_2steps'] = checkIntervalLength(ResultDict['seizure_50'], 6*fs) # 6s
['seizure_4steps'] = checkIntervalLength(ResultDict['seizure_50'], 10*fs)# 10s
['seizure_6steps'] = checkIntervalLength(ResultDict['seizure_50'], 14*fs)# 14s
['seizure_8steps'] = checkIntervalLength(ResultDict['seizure_50'], 18*fs)# 18s
['seizure_10steps'] = checkIntervalLength(ResultDict['seizure_50'], 22*fs)# 18s
['numOfSeizure_2steps'] = len(ResultDict['seizure_2steps'])
['numOfSeizure_4steps'] = len(ResultDict['seizure_4steps'])
['numOfSeizure_6steps'] = len(ResultDict['seizure_6steps'])
['numOfSeizure_8steps'] = len(ResultDict['seizure_8steps'])
['numOfSeizure_10steps'] = len(ResultDict['seizure_10steps'])

