# Exploring  Key Factors Behind Life Insurance Policy Lapses and What Insurers Can Do To Prevent Them

## Table Of Contents
- [Project Overview](#project-overview)
- [Business Context](#business-context)
- [Business Objectives](#business-objectives)
- [Dataset And Tools](#dataset-and-tools)
- [Methodology](#Methodology)
- [Key Insights](#key-insights)
- [Limitations](#limitations)
- [Conclusions And Recommendations](#conclusions-and-recommendations)
- [Appendix A: Procedures and Code](appendix-a-procedures-and-code)
  

  
  ![an image of black man or woman that demonstrate an life insurance policy has lapsed (1)](https://github.com/user-attachments/assets/0f52195e-0ea6-4873-b3a1-6ac2bec90715)

  



### Project Overview

Have you ever signed up for something important, only to stop halfway because life got in the way?

It happens. And it’s surprisingly common with life insurance.

This project investigates why so many people let their life insurance policies lapse and what insurers can do to reduce it. Using a real dataset and exploratory data analysis in R, I identified key trends and proposed data-driven, real-world solutions


### Business Context

Life insurance is more than a financial product. It’s a safety net for families.
When policies lapse:

- Customers lose critical financial protection.

- Insurers lose revenue and customer trust.

#### This analysis focused on three main questions:


- How do lapse rates vary across different age groups?

- Does payment frequency influence lapse rates?

- Do shorter policy terms lapse more often than longer ones?


### Business Objectives

- Identify which age groups are most prone to policy lapses.

- Analyze how payment frequency impacts lapse rates.

- Evaluate whether shorter policy terms lead to higher lapse rates.

- Recommend actions insurers can take to reduce lapse rates.


### Dataset And Tools

1. Dataset:(Life Insurance Dataset Kaggle[(https://www.kaggle.com/datasets/balgdrum/life-insurance-policy-data)]

2. Key Variables Analyzed 

- Age

- Payment Frequency (monthly, quarterly, semi-annually, annually and single premium)

- Policy Term

- Policy Status


3. Tools Used

- Programming Language: R

- Libraries: tidyverse, dplyr and  ggplot2

- Techniques: Data cleaning, data transformation, grouping variables, visualization with ggplot

### Methodology

1. Data Preparation Process

- Removed duplicates.

-	Dropped unused columns (BENEFIT, Premium) for this analysis.

- Filtered out ages below 18 years

 2. Data Transformation 

- Grouped ENTRY AGE into: 18–30, 31–50, 51+.

- Grouped Policy Year into: Short (≤ 5 years) and Long (> 5 years)

- Grouped  “Lapse”, “Surrender”, “Expired” to Lapsed and  “Inforce” to  Active



### Key Insights

Below are the main insights uncovered during the analysis, accompanied by visualizations generated in


              ###  AGE GROUP VS LAPSE RATE
        
<img width="511" height="271" alt="Image" src="https://github.com/user-attachments/assets/37a8ffd7-725a-4980-a9a3-48e3357b6a92" />

- Younger people(ages 18-30)are more likely to stop paying premiums on their policies over a period.

- Older people(50+) are more likely to keep their policies


             ### LAPSE RATE (%) ACROSS PAYMENT FREQUENCY

<img width="511" height="271" alt="Image" src="https://github.com/user-attachments/assets/00f3913a-1ac5-47fc-b4cf-5dc866a21440" />




- Policies paid all at once or once a year are more likely to stay active.

- Those with monthly or quarterly payments are more likely to stop paying their premiums.


             ### LAPSE RATES BY POLICY TERM: SHORT-TERM VS LONG-TERM POLICIES

             
<img width="511" height="271" alt="Image" src="https://github.com/user-attachments/assets/d9a41e89-db92-413f-a155-7b317b4c334b" />




- Policies with shorter terms (≤5 years) have a higher likelihood of lapsing.

- Longer-term policies are more stable.


### Limitations

Of course, no project is perfect. Mine had limitations:

The dataset didn’t include personal-level details like income, education or employment status. It also didn’t account for premium increments over time.

This means the analysis focused more on general trends than individual financial behavior, something I noted as an area for future research.



### Conclusions And Recommendations  

I didn’t just want to stop at the "what." I thought, 'How can this help someone in the real world?'

So I suggested two practical ideas:

- A “Resilience Rider” –  a feature that offers up to two years of grace to clients facing financial hardship (such as job loss), provided the policy is active at the time of request. This benefit would come with conditions, including a specified policy value threshold for eligibility. Additionally, if the client requests any benefits during the grace period, the insurer would receive a 20% share of the withdrawn amount.

- Loyalty incentives – simple rewards like gift vouchers and health checkups to encourage clients to keep their policies active over time.

By acting on these data-driven insights, insurers can reduce lapses, retain more customers and provide continuous protection creating a win–win for both the company and policyholders


         





### Appendix A: Procedures and Code
For readers who would like to see how the analysis was performed, the steps and corresponding R code used in the project are provided below.

#### 1. Loading  Libraries and the life insurance data(kaggle) into R 

```{r}
install.packages("tidyverse")            ## Load necessary libraries
library(tidyverse)
library(ggplot2)
library(dplyr)
library(readr)
life_insurance_data <-read_csv("life insurance data.csv")   ## Import data
 ```

#### 2. Data Cleaning
 

```{r}
# Remove duplicates
life_insurance_unique <-life_insurance_data[!duplicated(life_insurance_data),]   
```

```{r}
#  Handle missing values
colSums(is.na(life_insurance_unique))   
```

```{r}
# Filter ages (remove under ages below 18)
life_insurance_unique <- life_insurance_unique %>% filter(`ENTRY AGE` >= 18)
```

```{r}
 # Drop columns we won’t analyze (BENEFIT, Premium)
life_insurance_reduced <- subset(life_insurance_unique, select = -c(BENEFIT, Premium))
```

```{r}
 # Rename columns for clarity
life_insurance_reduced <- life_insurance_reduced %>%
  rename(
    age =`ENTRY AGE`,
    payment_frequency = `PAYMENT MODE`,
    policy_term =`Policy Year`)
```

```{r}
# Keep a clean dataset for further processing
 life_insurance_rename = life_insurance_reduced
```



```{r}
# Grouping age into categories 
life_insurance_rename <- life_insurance_rename %>%
  mutate(age_group = cut(age,
                         breaks = c(18, 30, 50, Inf),
                         labels = c("18–30", "31–50", "51+")))
```

 
```{r}
# Renaming column names to lowercase
names(life_insurance_rename) <- tolower(names(life_insurance_rename))
```


```{r}
 Check the summary for ages 
summary(life_insurance_rename$age_group)   ## there are missing values from the checks 
```

```{r}
# Remove rows with missing age group
life_insurance_clean <- life_insurance_rename%>%
  
  filter(!is.na(age) & age >= 18 & age <= 100) %>%
 
  mutate(age_group = cut(
    age,
    breaks = c(17, 30, 50,Inf),
    labels = c("18–30", "31–50", "51+")
  ))
```

Policy status in  this  data is the combination of “Lapse”, “Surrender”, “Expired” ,"Death" and "Inforce".
To calculate for  lapse, we need to calculate the  lapse variable 
```{r}
# Grouped  “Lapse”, “Surrender”, “Expired” to Lapsed and  “Inforce” to  Active

life_insurance_lapse <- life_insurance_clean %>%
  filter(`policy status` != "Death") %>%               
  mutate(`policy status` = case_when(
    `policy status`%in% c("Lapse", "Surrender", "Expired") ~ "Lapsed",
    `policy status` == "Inforce" ~ "Active",
    TRUE ~ "Other"
  ))                
```

### 3. Analysis
3.1 (Lapse Rate by Age Group)

```{r}
#  Calculating lapse_by_age
lapse_by_age <- life_insurance_lapse %>%
  group_by(age_group, `policy status`) %>%
  summarise(count = n(), .groups = "drop")
  ```
```{r}
 # Calculate lapse rate by age group
lapse_rate_by_age <- lapse_by_age %>%
  group_by(age_group) %>%
  mutate(
    total = sum(count),
    lapse_rate = round((count / total) * 100, 1)
  ) %>%
  filter( `policy status` == "Lapsed")  # keep only lapse rate rows
  ```

```{r}
#### Visualizing lapse rate by age group(plot)
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

 3.2 (lapse rate by payment frequency)

```{r}
## Grouped Policy Terms: Binned policy_term into “Short (≤ 5 yrs)” vs. “Long (> 5 yrs)
lapse_rate_by_freq <- life_insurance_lapse %>%
  group_by(payment_frequency, `policy status` ) %>%
  summarise(count = n(), .groups = "drop") %>%
  group_by(payment_frequency) %>%
  mutate(
    total      = sum(count),
    lapse_rate = round(count / total * 100, 1)
  ) %>%
  filter(`policy status`== "Lapsed")  # only need the Lapsed rows
```

```{r}
## visualizing(lapse rate by payment frequency)
ggplot(lapse_rate_by_freq, aes(x = payment_frequency, y = lapse_rate, fill = payment_frequency)) +
  geom_col() +
  geom_text(
    aes(label = paste0(lapse_rate, "%")), vjust = -0.5, size = 4) +  # Labels on top of bars
  scale_fill_manual(values = c(
    "Monthly"         = "indianred1",
    "Quaterly"       = "sandybrown"  ,
    "Annually"        = "mediumturquoise",
    "Semiannually"    = "deepskyblue2",
    "Single Premium"  = "mediumpurple3" 
  )) +
  scale_x_discrete(labels = c(
    "Monthly"         = "Monthly",
    "Quarterly"       = "Quarterly",
    "Annually"        = "Annually",
    "Semiannually"    = "Semi-\nAnnually",
    "Single Premium"  = "Single\nPremium"
  )) +
  labs(
    title = "Lapse Rate by Payment Frequency",
    x     = "Payment Frequency",
    y     = "Lapse Rate (%)"
  ) +
  ylim(0, max(lapse_rate_by_freq$lapse_rate) + 20) +
  theme_minimal(base_size = 14) +
  theme(
    legend.position = "none",
    axis.text.x     = element_text(angle = 45, hjust = 1)
  )
```

 3.3 (Lapse Rate by Policy Term)

```{r}
life_insurance_term <- life_insurance_lapse %>%
  mutate(
    term_group = if_else(             ### grouping to <=5 & >5year term
      policy_term <= 5,
      "Short (≤5 yrs)",
      "Long (>5 yrs)"
    )
  )
  
 term_counts <- life_insurance_term %>%
  group_by(term_group, `policy status`) %>%
  summarise(count = n(), .groups = "drop")     ## Count policies by term_group and lapses
  
  
  lapse_rate_by_term <- term_counts %>%         ## Calculating total policies per term_group and compute lapse rate 
  group_by(term_group) %>%
  mutate(
    total      = sum(count),                  # total policies in each term_group
    lapse_rate = round(count / total * 100, 1)  # % that are lapsed
  ) %>%
  filter(`policy status` == "Lapsed")       # focus on lapse rates only

```


```{r}
## Visualizing term_group by lapse_group
ggplot(lapse_rate_by_term, aes(x = term_group, y = lapse_rate, fill = term_group)) +
  geom_col() +
  geom_text(
    aes(label = paste0(lapse_rate,"%")),
    vjust = -0.8, 
    size = 4
  ) +             
  scale_fill_manual(values = c(
    "Short (≤5 yrs)" = "indianred1",
    "Long (>5 yrs)"  = "sandybrown"
  )) +
  labs(
    title = "Lapse Rate by Policy Term",
    x     = "Policy Term",
    y     = "Lapse Rate (%)"
  ) +
  ylim(0, max(lapse_rate_by_term$lapse_rate) + 8) + 
  theme_minimal(base_size = 14) +
  theme(legend.position = "none")
```





 
  



  









