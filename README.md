# EXPLORING KEY FACTORS BEHIND LIFE INSURANCE POLICY LAPSES AND WHAT INSURERS CAN DO TO PREVENT IT

# Project Overview
Have you ever signed up for something important… but had to stop halfway because life got in the way?

It happens . And it’s surprisingly common with life insurance.

This project investigates why so many people let their life insurance policies lapse and what insurers can do to reduce it. Using a real dataset and exploratory data analysis in R, I identified key trends and proposed data-driven, real-world solutions


# Business Context

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

## Checking  and removing duplicate
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


## Drop columns we won’t analyze (BENEFIT, Premium)
```{r}
life_insurance_reduced <- subset(life_insurance_unique, select = -c(BENEFIT, Premium))
```


## Rename columns for clarity
```{r}
library(dplyr)
life_insurance_reduced <- life_insurance_reduced %>%
  rename(
    age =`ENTRY AGE`,
    payment_frequency = `PAYMENT MODE`,
    policy_term =`Policy Year`)
```
## Keep a clean dataset for further processing
```{r}
life_insurance_rename = life_insurance_reduced
```

Why? Grouping continuous variables like age into categories
# makes trends easier to interpret for business decisions
```{r}
life_insurance_rename <- life_insurance_rename %>%
  mutate(age_group = cut(age,
                         breaks = c(18, 30, 50, Inf),
                         labels = c("18–30", "31–50", "51+")))
```

## Renaming columnames to lowercase
```{r}
names(life_insurance_rename) <- tolower(names(life_insurance_rename))
```

## Check summary for ages 
```{r}
summary(life_insurance_rename$age_group)   ## there are missing values from the checks 
```

## Remove rows with missing age group
```{r}
life_insurance_clean <- life_insurance_rename%>%
  
  filter(!is.na(age) & age >= 18 & age <= 100) %>%
 
  mutate(age_group = cut(
    age,
    breaks = c(17, 30, 50,Inf),
    labels = c("18–30", "31–50", "51+")
  ))
```

## Policy status in  this  data is the combination of “Lapse”, “Surrender”, “Expired” ,"Death" and "Inforce".
To calculate for lapse, we need to calculate for a lapse variable 
```{r}
life_insurance_lapse <- life_insurance_clean %>%
  filter(`policy status` != "Death") %>%               
  mutate(`policy status` = case_when(
    `policy status`%in% c("Lapse", "Surrender", "Expired") ~ "Lapsed",
    `policy status` == "Inforce" ~ "Active",
    TRUE ~ "Other"
  ))                ## Grouped  “Lapse”, “Surrender”, “Expired” to Lapsed and  “Inforce” to  Active
```
# Key Question 1(Lapse Rate by Age Group)

## calculating lapse_by_age
```{r}
lapse_by_age <- life_insurance_lapse %>%
  group_by(age_group, `policy status`) %>%
  summarise(count = n(), .groups = "drop")
  ```
## Calculate lapse rate by age group
```{r}
lapse_rate_by_age <- lapse_by_age %>%
  group_by(age_group) %>%
  mutate(
    total = sum(count),
    lapse_rate = round((count / total) * 100, 1)
  ) %>%
  filter( `policy status` == "Lapsed")  # keep only lapse rate rows
  ```
## Visualizing lapse rate by age group
```{r}
ggplot(lapse_rate_by_age, aes(x = age_group, y = lapse_rate, fill = age_group)) +
  geom_col() +
  geom_text(
    aes(label = paste0(round(lapse_rate, 1), "%")),
    vjust = -0.3, 
    size = 4
  ) +
  scale_fill_manual(values = c("indianred1",  "sandybrown", "mediumturquoise")) +
  labs(
    title = "Lapse Rate by Age Group",
    x = "Age Group", 
    y = "Lapse Rate (%)"
  ) +
  ylim(0, max(lapse_rate_by_age$lapse_rate, na.rm = TRUE) + 10) +  # Add 10% headroom
  theme_minimal(base_size = 14) +
  theme(legend.position = "none")
```



 
  



  









