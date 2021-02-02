[[_TOC_]]

# Description of the Mathematics of The Top Focus Area Impacts

This is a distillation of the work in the Excel files attached to #56242, specifically the _Documentation Of Process.xlsx_ and  _Documentation of Process - Revised 1.28.21.xslx_ files

This document describes the derivation of the Impact score for each category.  Three steps are required:

- Calculate the average Top 2 Box Percentage for the Positive ($A_{Cn}$ and Negative $B_{Cn}$ respectively) for each category
- Calculate the Gap for each Category ($G_{Cn}$)
- Calculate the Impact for each Category ($I_{Cn}$)

These are described algorithmically in this document, but could also be accomplished with aggregation in SQL (or NoSQL) using database engines that support the required aggregation.

The value returned by the backend service is an array of the Impacts for each category ($I_{Cn}$), A response value might look something like the following: 


```
{
  Impacts: [
            {Call_Handling_Friendliness: 0.0495}, 
            {Call_Handling_Helpfulness: 0.0785}, 
            {BankProc_Issues_or_Problems: 0.1008}, 
            {Call_Handling_Level_of_Service: 0.0931},
            {"Resolution_Answer_Question": 0.0524}, 
            {"Resolution_Problem_Incidence": 0.1233},
            {"Resolution_Providing_Solution": 0.1393},
            {"Call_Handling_Speed_of_Service": 0.1299},
            {"Call_Handling_Knowledge": 0.0784},
            {"Call_Handling_Professionalism": 0.0243 },
            {"BankProc_Loan_Processing": 0.1295},
            {Check_Save_CD_General": 0.1266},
            {Overall_Experience_Communication": 0.2153},
            {BankProc_Ease_of_doing_business": 0.2241},
            {Credit_Card_CC_General": 0.1363},
            {Fees_Overdraft_and_NSF": 0.1465},
            {Call_Handling_Transfers": 0.2287},
            {Mortgage_Mortgage_General":0.0979}
           ]
}
```


## Sentiment versus OSAT calculation

Given rows with these columns:

- Satisfaction 
- Overall Sentiment
- C1 Sentiment
- C2 Sentiment
- C3 Sentiment
....
- Cn Sentiment


Satisfaction is Q5 in the AllRecodes sheet in the attachment _AllRecodes.xslx_ found in #56242

### For the Positive Sentiments (one per category)

#### Calculate the Ratio of Top Box 2 for Each Category Cn - **$A_{Cn}$**

Take the count of number of rows where the Sentiment value of the Category Cx is Positive (greater than 1.0) and divide this by the total number of rows where the Sentiment value of the Category Cx is Positive (Sentiment > 1.0).

Let's define this variable as 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;$
A_{C1}
$ 

for Categories 1, 2, 3, 4, ... n we would have the values  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;$
A_{C1}, A_{C2}, A_{C3}, A_{C4}, ... A_{Cn}
$ 

So the expression for a Category n is

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;$
A_{Cn} = \dfrac{Count\ of\ Rows\ where\ ((Sentiment\ of\ {Cn}\ > 0)  \land (Q5\ \in \{4,5\}))}{Count\ of\ Rows\ where(Sentiment\ of\ {Cn}\ > 0)}
$

*Note: this is the 94% in the _Documentation of Process - Revised 1.28.21.xslx_ file, Sheet _Data Processing From CXS_ 

e.g.

|category|FREQ|tQ5|Variable|
|--|--|--|--|
|1|2595|94%|pos1|


### For the Negative Sentiments (one per category)

#### Calculate the ratio of Top Box 2 for Each Category Cn - **$B_{Cn}$** For Each Category

Take the count of number of rows where the Sentiment value of the Category Cx is Negative (less than -1.0) AND the value of Q5 is in the Top 2 Box (e.g. 4,5 out of a range of 1-5) and divide this by the total number of rows where the Sentiment value of the Category Cx is Negative (less than -1.0).

Let's define this variable as 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;$
B_{C1}
$ 

for Categories 1, 2, 3, 4,... n we would have the values

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;$
B_{C1}, B_{C2}, B_{C3}, B_{C4}, ... B_{Cn}
$ 

So the expression for a Category n is

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;$
B_{Cn} = \dfrac{Count\ of\ Rows\ where\ ((Sentiment\ of\ {Cn}\ < 0)  \land (Q5\ \in \{4,5\}))}{(Sentiment\ of\ {Cn}\ < 0)}
$

*Note: this is the 20% in the _Documentation of Process - Revised 1.28.21.xslx_ file, Sheet _Data Processing From CXS_

### Calculate the Gap for Each Category, $G_{Cn}$

Take the difference of the value of each **$A_{Cn}$** and **$B_{Cn}$**

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;$
G_{Cn} = A_{Cn} - B_{Cn}
$

|category|FREQ|tQ5|Variable|
|--|--|--|--|
|1|190|20%|neg1|

### Impact Score **$I_{Cn}$**

Impact Score is the Product of the Gap  ($G_{Cn}$) and the Ratio of the Negative Sentiment Count divided by the Total Count for that Category

From the _Documentation of  Process - Revised 1.28.21.xslx_ file (Data Processing from CXS sheet) 

"Product of Frequency of Negative mentions of Category and the Gap in Overall Metric when Category is mentioned negatively versus when mentioned positively"

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;$
I_{Cn}  = G_{Cn} \times (NCount_{Cn}/TotalCount_{Cn})
$



The Impact Scores are returned by the backend API.

Refs: see https://dev.azure.com/convergys-cx/IAT/_wiki/wikis/IAT%20Enterprise%20Service%20Platform.wiki/1534/Overview-of-survey-calculations-metrics-kpi for details of terms

