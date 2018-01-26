## SBB instruction
####Step1: Translate Mobileye data and MIDG data
Mobileye data is encoded by its own CAN protocol, MIDG data is binary file.

####Srep2: Extract and down-sample MIDG data
MIDG provides data in 25Hz and some of the data is not used in the SBB program.

####Step3: Synchronize Mobileye data, MIDG data and Video

 MIDG records data using GPS timestamp, while Mobileye and camera are recording data using laptop time or the camera time set by the user. Thus we cannot accurately synchronize these data by timestamp conversion. Instead, we synchronize these data by comparing the information they contain. 

####Step4: Run the SBB code