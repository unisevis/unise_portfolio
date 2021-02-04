
# Practice project
Click on the link below to view project notebook

[>> Practice DataCamp project: The discovery of handwashing <<](https://nbviewer.jupyter.org/github/unisevis/unise_portfolio/blob/main/datacamp%20projects/Dr.%20Semmelweis%20and%20theDiscovery%20of%20Handwashing.ipynb)

---

# Current project: Is VR game an appropriate tool to measure children's wayfinding skills?
This is my thesis project that examines whether the Congolese children's wayfinding skills can be more conveniently assessed with a virtual reality game. Working with research teams in the United States, Germany, and the Netherlands, my task is to find the answer by digging into the data avaiable. 

Technology used: R (dplyr, ggplot, ggeffect, lmer, glmmTMB, car, sjplot), Excel (pivot), powerBI.

### Step 1 define
- Researched factors affecting navigation skills.
- Developed analysis plan.

### Step 2 data preprocessing
- Cleaned data to maintain good data quality.
- Aggregated variables from master .csv file in Excel (pivot table) and R (dplyr).

<details>
  <summary>Click to see sample code</summary>
  
```r
library(dplyr)

setwd("/~/")

#importing data
data=read.csv(file="/~/.csv", fileEncoding="UTF-8-BOM") 

# names of participants whose data points need recoding 
touch_exp = c("A,S,D,F,G,H,J,K,L") 

#create an object to contain the desired data   
t= 
data %>% 
#selecting variables
  select(tribe = TRIBE, name = NAME, subj = SUBJ_ID, age = Age_2019, sex = Sex, 
         stage = STAGE_NAME, sess_name = SESS_NAME, trial = TRIAL, status = STATUS, 
         time = TRAVEL_TIME, SESS_DESC, dist = OPTIMAL_DIST_BFC) %>% 
#filtering out unwanted data
  filter(stage != "Exploration", 
         trial != "4") %>%  
#create new variables based on existing variables
  mutate(is_st3 = stage=="Stage3", 
         is_tr3 = trial == "3",
         st3tr3 = is_st3*is_tr3) %>%
#filtering out unwanted data
  filter(st3tr3 !=1) %>%  
#re-coded values  
  mutate(stage_num = as.integer(gsub(pattern = "Stage", replacement ="", x = stage))) %>% 
#make character values integer  
  mutate(ses_num = as.numeric(c("A"="1", "B"="2","C"="3")[sess_name])) %>%
#group data by variable  
  group_by(tribe, name, stage, ses_num, trial) %>% 
#calculate new variable  
  mutate(is_touch = name %in% touch_exp,  
         is_st1 = stage =="Stage1",  
         session = ses_num+(is_touch*is_st1)) %>% 
#ungroup the data before saving
  ungroup()%>%
#choose variables to be included in the new data frame
  select(tribe, name, subj, age, sex, stage_num, session, trial, dist, status, time, st3tr3, is_touch, is_st1)
 
#save file as .csv
write.csv(t,"/~/.csv", row.names = FALSE) 
```
</details>

### Step 3 exploratory data analysis
- Visualized raw data with an overview dashboard with PowerBI to obtain better understanding.

<img src="https://github.com/unisevis/unise_portfolio/blob/main/images/example%20power%20bi%20dashboard.png" width="550" height="300">

### Step 4 data analysis (R)
- Checked model assumptions with plots.
- Built generalized linear mixed effects model.

### Step 5 data visualization and communicating results
- Visualized model results using ggeffect package in R
- Created summary tables of important statistics

<details>
  <summary>Click to see sample code</summary>
  
```r

#create a vector object containing model prediction 
pred.trialses = ggeffect(linear.full, terms = c("z.session","trial"))

#create a plot from the prediction object
trialses = plot(pred.trialses, add.data =T, jitter = 0.07) + 
#add labels to x and y axes and title
  labs(x = "Sessions", y="VR linearity score", 
       title = "Predicted VR linearity scores by trial and session",
#tells R to create separate lines for each trial      
       color="Trial")+ 
#rescale the y axis 
  scale_y_continuous(limits = c(0,1))+
#because the variable on the x axis was z-transformed, relabel the x axis 
  scale_x_continuous(breaks = c(-1,0,1,2), labels = c(1,2,3,4))

```
</details>

<img src="https://github.com/unisevis/unise_portfolio/blob/main/images/mod2%20Intx%20trial%20and%20session.png" width="400" height="300">

