# EXPLORING KEY FACTORS BEHIND LIFE INSURANCE POLICY LAPSES AND WHAT INSURERS CAN DO TO PREVENT IT

# Project Overview
Have you ever signed up for something important… but had to stop halfway because life got in the way?

It happens . And it’s surprisingly common with life insurance.

This project investigates why so many people let their life insurance policies lapse and what insurers can do to reduce it. Using a real dataset and exploratory data analysis in R, I identified key trends and proposed data-driven, real-world solutions

#️ Business Context

Life insurance is a safety net but when people stop paying their premiums, both they and the insurer lose out.

By understanding which customers are more likely to lapse and why, insurers can design better products and communication strategies to keep people protected and maintain their customer base.

This analysis focused on three main questions:

• How do lapse rates vary across different age groups?

• Does payment frequency influence lapse rates?

• Do shorter policy terms lapse more often than longer ones



## Loading the life insurance data(kaggle) into R
```{r}
install.packages("tidyverse")
library(tidyverse)
life_insurance_data <-read_csv("life insurance data.csv")
```
## Inspecting the structure of the data
```{r}
glimpse(life_insurance_data)          ## View column types and samples     
```

##Checking for duplicate and removing duplicate
```{r}
any(duplicated(life_insurance_data))  ## check for duplicate
sum(duplicated(life_insurance_data))  ## gives the total number of duplicates in the data 
```

## Taking duplicates out of the life data 
```{r}
life_insurance_unique <-life_insurance_data[!duplicated(life_insurance_data),]   ## removing duplicate
```
## checking for missing values in life_insurance_unique data
```{r}
anyNA(life_insurance_unique)
```
## Checking to see where specifically in the data can we find the missing values 
```{r}
colSums(is.na(life_insurance_unique))   ## show  column names with count of missing values(missing values in benefits and premiums)
```

## The entry age to sign up to a life insurance policy is 18 years or more . so i removed ages less than 18 years from “life_insurance_unique” data set . 
```{r}
life_insurance_unique <- life_insurance_unique %>% filter(`ENTRY AGE` >= 18)
```


## Premiums and benefits contains missing values in the data .  Both  will  not be needed in this analysis  , hence we will take them  out 
```{r}
life_insurance_reduced <- subset(life_insurance_unique, select = -c(BENEFIT, Premium))
```




