
# MRTS FLOW PREDICTION


## Introduction

Mass Rapid Transit Systems (MRTS), also referred to metro system,
generate a lot of data. These data have a potential to give useful visualization. Analyzing it, we can come up with applications and solutions to improve and benefit these systems. One of such applications is of predicting passenger ridership between any given two stations in the metro network. The historical data visualization helps to design the system infrastructure. Predicting ridership at a station helps on many fronts and enables real time decision making with a potential to optimize resources. Some useful actions for the metro management with the daily ridership prediction are:

-   Scheduling more trains on busy stretches automatically.
-   Helping advertising companies do bidding for ad spots in real-time (on digital displays)
-   Aiding managers to regulate staffing more effectively

## Methodology

 This work consists of analyzing the Automatic Fare Collection (AFC)
 data of Kochi Metro Rail Limited (KMRL). Useful visualizations based
 on the data have been done. Importantly, prediction of passenger flow
 between a pair of stations has been demonstrated using a novel method
 outlined by the paper:
 <https://www.hindawi.com/journals/jat/2018/5942763/

 Reason for choosing this paper:

-   The metro systems the study (paper) was based on, have very similar
     characteristics to Kochi Metro.

-   The goal of the paper matches with ours.

-   Not many papers are available doing analysis of data generated by
     metro systems.

## Data Description

 KMRL operates in the heartland of Cochin City. At the time of this
 work, the Kochi Metro network was linear, stretching from Maharajas
 College to Aluva.

 ![Image result for kochi metro
 map](https://github.com/aye21874/Metro_Flow_Prediction_Code/blob/master/image1.jpeg?raw=true)

 Figure 1

 The data available is of Automatic Fare Collection (AFC) system. This
 data is collected by the electronic gates installed at every station,
 through which every passenger has to go through for entering or
 exiting a station by submitting their tickets.

 The AFC data is of the years June 2017 to December 2018. The data is
 stored in form of Excel sheets.

 Important fields of the data are:

-   Station: Specifies which of the 16 stations the data entry is
     associated with.

-   Fare product: Specifies for what purpose the person enters the
     station e.g. Single journey trip, Round journey trip, station
     access card (for staff) etc.

-   Ticket/Card Number: A unique hashed alphanumeric sequence given to
     identify each row entry and thus each bought ticket uniquely.

-   Transaction Type: Takes values Entry or Exit to ascertain whether
     the person entered the station or exited.

-   Date and Time: Give the date and time of the entry or exit of the
     person for a station.

## Some Visualizations

4.1 **Station wise ridership**: The net inflow and outflow from stations
was done to understand the travel demand at different stations.

 **Explanation about how the data is processed:**

 Data was loaded into a pandas dataframe. Data was filtered by date
 (over here December 2017) was taken. Data was separated by Transaction
 type being 'Entry' or 'Exit'. Afterwards, the filtered data was
 grouped by stations. Then bar plots are made based on entry and exit
 data. The passenger entering different station is shown in Fig 2 and
 the passengers exiting different stations are shown in Fig 3.

 ![C:\\Users\\MAHE\\AppData\\Local\\Microsoft\\Windows\\INetCache\\Content.MSO\\D7E68ECF.tmp](https://github.com/aye21874/Metro_Flow_Prediction_Code/blob/master/image2.png?raw=true)
 
Figure 2

 ![C:\\Users\\MAHE\\AppData\\Local\\Microsoft\\Windows\\INetCache\\Content.MSO\\613BD0AD.tmp](https://github.com/aye21874/Metro_Flow_Prediction_Code/blob/master/image3.png?raw=true)

 Figure 3

4.2 **Travel pattern during the day**: The inflow and outflow of
passengers at different stations was done to give the flow of the
passengers during different hours of the day.

 **Explanation about how the data is processed:**

A function was built to give passenger inflow/outflow for all station
for a day or a range of dates. Taking inputs as the range of dates to
generate the output for and whether to give outflow or inflow volume.
Data is aggregated based on transaction time with intervals of 1 hour
(option given in function). Then the data is filtered based on entry or
exit, after which a line plot is done. In figure 4 the visualization for
a single day in December 2017 of outflow volume of 16 stations is
presented.

 ![C:\\Users\\MAHE\\AppData\\Local\\Microsoft\\Windows\\INetCache\\Content.MSO\\C5C46BB6.tmp](https://github.com/aye21874/Metro_Flow_Prediction_Code/blob/master/image4.png?raw=true)

Figure 4

## 5 Ridership Prediction

5.1 For predicting the passenger ridership, Edapally is chosen as the
origin and M.G Road as the destination station. The model chosen will
also consider adjacent stations of this OD (Origin Destination) pair.
The stations adjacent to Edapally stations are Changampuzha and
Pathadipalam while the stations adjacent to M.G Road are Lissie and
Maharajas College.

5.2 Explanation of Origin-Destination (OD) Model

 Instead of only considering past values of the flow between Edappaly
 and M.G Road station and predicting based on such information, [the
 model takes into consideration the effect which the traffic of
 adjacent stations is having on the flow of our concerned OD.]{.ul}

 Justification: We assume that the traffic of the adjacent OD pairs and
 that of our concerned OD are positively correlated. We do this because
 of the intuition that if adjacent ODs are busy then that indicates
 that the target OD (Edappaly and M.G Road) is busy too.

 Thus we are also taking the past data of adjacent OD pairs and
 including them in our feature vector. There will be thus nine entries
 corresponding to each OD pair (because there are three origins
 Edapally, Changampuzha, Pathadipalam and three destinations M.G Road,
 Lissie and Maharajas College).

  **Index**   **Origin**          **Destination**     **Number of passengers travelling(on a date)**
  ----------- ------------------- ------------------- ------------------------------------------------
  **0**       Changampuzha Park   Lissie              53
  **1**       Changampuzha Park   M.G Road            152
  **2**       Changampuzha Park   Maharajas College   326
  **3**       Edapally            Lissie              751
  **4**       Edapally            M.G Road            1091
  **5**       Edapally            Maharajas College   2570
  **6**       Pathadipalam        Lissie              42
  **7**       Pathadipalam        M.G Road            98
  **8**       Pathadipalam        Maharajas College   201

 Table 1

 Our feature vector would be holding values like the column with the
 heading "Some Date" and the value we're interested in predicting is of
 5^th^ row i.e. Edapally to M.G Road.

 After processing the data and making training samples according to
 different dates we'll feed the processed data to a Kneighborsregressor
 algorithm to predict the OD flow for Edapally to M.G Road for the next
 date.

5.3 Processing the Data

 Steps:

1)  Data is filtered by date.

2)  Two dataframes (suppose A and B) are made based on 'Transaction
     type' being entry or exit.

3)  All unnecessary columns are dropped.

4)  The dataframes A and B are merged (natural join) into a new
     dataframe (suppose C) based on 'Ticket/Card number' ( as this
     serves as an identification for a single passenger).

5)  All duplicates rows in C are dropped.

6)  Data in C is grouped by stations of A and station of B.

7)  Data is then filtered on the condition that stations of A should be
     in \[\'Edapally\',\'Pathadipalam\',\'Changampuzha Park\'\] and
     station of B should be in \[\' M.G Road \',\' Lissie \',\'
     Maharajas College \'\].

8)  These steps are done for every date (available from the original
     data).

9)  **Weekends** are ignored as we assume they have a different pattern
     compared to working days.

 Kindly find the data processing implementation in kmrl1.ipynb
 *(*[*https*://github.com/aye21874/Metro_Flow_Prediction-Code-](https://github.com/aye21874/Metro_Flow_Prediction-Code-))

5.4 Making the input data

 Our feature vector contains data for target OD (Edapally to M.G Road)
 as well as neighboring ODs. In addition to this each our input data
 consists of 5 consecutive dates. Thus our feature vector becomes a
 feature matrix. This matrix is made by concatenating the last column
 of Table 1 for 5 consecutive dates. The feature matrix is depicted in
 Table 2.

  **Index**   **2017/10/03**   **2017/10/04**   **2017/10/05**   **2017/10/06**   **2017/10/09**
  ----------- ---------------- ---------------- ---------------- ---------------- ----------------
  **0**       20               30               32               49               29
  **1**       42               105              87               107              81
  **2**       154              283              258              274              184
  **3**       189              289              336              308              257
  **4**       361              529              497              523              411
  **5**       794              1146             1126             1212             989
  **6**       27               27               44               37               40
  **7**       30               70               64               61               68
  **8**       81               134              140              103              110

 Table 2

 The feature matrix is again converted into a single column feature
 vector by appending one column below the other hence generating a
 feature vector with 45 (9\*5) rows. The desired output is passenger
 flow value of target OD for the next date. Over here in Table 2 we
 would be interested in finding value of row with index 4 for date
 2017/10/10. We would be interested in index 4 as it corresponds to our
 target OD as shown in Table 1. We do this for every date and get 320
 samples from the available data.

5.5 Training the Model

-   After generating the input data, we append target output to it as
     the K-nearest Neighbors Regression Algorithm is a Supervised
     learning Algorithm.

-   After doing this we split the data into training and testing (80:20)
     and apply **Principal Component Analysis** (PCA) on the training
     samples.

-   We apply PCA because the feature vector has a very high
     dimension (45) and most probably would contain redundant
     information, increasing the uncertainty of the prediction. Thus
     applying PCA captures the variations in lower dimensions.

-   We take the first 10 principal components having the highest sample
     variance.

-   We train our model on this training data to predict the Target OD
     flow.

5.6 Model Evaluation

 The model is evaluated based on Mean absolute percentage Error (MAPE).
 The Trained model is given test set data and the difference between
 its output and the actual target value is compared by MAPE.

 After running the model on the test set we got an MAPE value of \~15.

 Kindly find the implementation with the quoted result in kmrl2.ipynb
 *(*[*https*://github.com/aye21874/Metro_Flow_Prediction-Code-](https://github.com/aye21874/Metro_Flow_Prediction-Code-))

 This means our prediction is off by 15% of the true value on average.
 Thus the model has a high potential of utilization in real world
 scenario and can be used by the Metro administrator.

## 6 Coursera Skills displayed and Conclusion:

 The coursera skills displayed are as under:

-   Mathematics for machine learning course helped me greatly in
     understanding machine learning concepts in general and the
     significance of PCA.

-   After going through the IBM python for data visualization, I was
     able to create meaningful visualizations that provide insight and
     help making decisions.

 The project demonstrated the working of a novel model where adjacent
 ODs data is also analyzed in making accurate prediction. The project
 applied a machine learning algorithm on the data generated by the
 Kochi Metro System and got an agreeable error rate on the predicted
 value comparable to the paper it referred. The model slightly under
 fits the data, mainly because the data generated is less. If more data
 is available better accuracy could be achieved.

 Still better results would be achieved in dense metro networks such as
 Delhi Metro, Bengaluru Metro etc as the model relies on correlations
 between adjacent ODs and considering these Metro's huge traffic,
 relations could be spotted more easily by the ML algorithms in those
 cases.
