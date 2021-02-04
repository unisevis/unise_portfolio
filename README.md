## Project 1: Is VR game an appropriate tool to measure children's wayfinding skills?
This is my thesis project that examines whether the Congolese children's wayfinding skills can be more conveniently assessed with a virtual reality game. Working with research teams in the United States, Germany, and the Netherlands, my task is to find the answer by digging into the data avaiable. 

Technology used: R (dplyr, ggplot, ggeffect, lmer, glmmTMB, car, sjplot), Excel (pivot), powerBI.

Step 1 define
- Researched factors affecting navigation skills.
- Developed analysis plan.

Step 2 data preprocessing
- Cleaned data to maintain good data quality.
- Aggregated variables from master .csv file in Excel (pivot table) and R (dplyr).

<details>
  <summary>Click to see sample code</summary>
  
```r
library(dplyr)

setwd("/~/")

data=read.csv(file="/~/.csv", fileEncoding="UTF-8-BOM") #importing data

touch_exp = c("A,S,D,F,G,H,J,K,L") # names of participants whose data points need recoding 
  
t=data %>% select(tribe = TRIBE, name = NAME, subj = SUBJ_ID, age = Age_2019, sex = Sex, 
                  stage = STAGE_NAME, sess_name = SESS_NAME, trial = TRIAL, status = STATUS, 
                  time = TRAVEL_TIME, SESS_DESC, dist = OPTIMAL_DIST_BFC)  #selecting variables
  filter(stage != "Exploration", #filtering out unwanted data
         trial != "4") %>%  
  mutate(is_st3 = stage=="Stage3", #create new variables based on existing variables
         is_tr3 = trial == "3",
         st3tr3 = is_st3*is_tr3) %>%
  filter(st3tr3 !=1) %>%  #filtering out unwanted data
  mutate(stage_num = as.integer(gsub(pattern = "Stage", replacement ="", x = stage))) %>% #re-coded values
  mutate(ses_num = as.numeric(c("A"="1", "B"="2","C"="3")[sess_name])) %>% #make character values integer
  group_by(tribe, name, stage, ses_num, trial) %>% #grouping data by variable
  mutate(is_touch = name %in% touch_exp,  
         is_st1 = stage =="Stage1",  
         session = ses_num+(is_touch*is_st1)) %>% #calculate new variable
  ungroup()%>%
  select(tribe, name, subj, age, sex, stage_num, session, trial, dist, status, time, st3tr3, is_touch, is_st1)
  #choose variables to be included in the new data frame

write.csv(t,"/~/.csv", row.names = FALSE) 
```
</details>

Step 3 exploratory data analysis
- Visualized raw data with an overview dashboard with PowerBI to obtain better understanding.

<img src="https://github.com/unisevis/unise_portfolio/blob/main/images/example%20power%20bi%20dashboard.png" width="550" height="300">

Step 4 data analysis (R)
- Checked model assumptions with plots.
- Built generalized linear mixed effects model.

Step 5 data visualization and communicating results
- Visualized model results using ggeffect package in R
- Created summary tables of important statistics

<img src="https://github.com/unisevis/unise_portfolio/blob/main/images/mod2%20Intx%20trial%20and%20session.png" width="400" height="300">
