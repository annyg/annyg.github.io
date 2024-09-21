---
layout: post
title: "Processing BRIEF-A data"
author: "ANYthings"
date: "2024-04-27"
tags: code datamanagement datascience
categories: datascience research blog
permalink: /posts/journalrank/
output:
  md_document:
    variant: gfm
    preserve_yaml: true
always_allow_html: true
knit: (function(inputFile, encoding) {
  rmarkdown::render(inputFile,
  encoding = encoding,
  output_file = file.path(paste0(
  "../annyg.github.io/_drafts/posts/",
  Sys.Date(),'-', substr(basename(inputFile), 1, nchar(basename(inputFile)) - 4),
  '.md'
  )))})
---

## Load the briefparser package

```r
library(briefparser)
```

    ## Loading required package: tidyverse

    ## Warning: package 'ggplot2' was built under R version 4.3.3

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.2     ✔ readr     2.1.4
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.0
    ## ✔ ggplot2   3.5.0     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.3     ✔ tidyr     1.3.0
    ## ✔ purrr     1.0.1

    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

## Create dummy data

```r
dummy_data <- generate_brief_a_dummy_data(n = 1000)

head(dummy_data)
```

    ##   age brief_q01 brief_q02 brief_q03 brief_q04 brief_q05 brief_q06 brief_q07
    ## 1  68         1         2         1         1         2         1         1
    ## 2  43         3         2         3         1         3         1         3
    ## 3  74         1         2         2         2         2         3         1
    ## 4  66         3         2         2         1         3         3         3
    ## 5  90         1         3         3         1         2         3         1
    ## 6  37         1         3         3         1         2         3         2
    ##   brief_q08 brief_q09 brief_q10 brief_q11 brief_q12 brief_q13 brief_q14
    ## 1         3         3         3         3         3         1         1
    ## 2         2         3         3         3         1         2         2
    ## 3         2         3         2         3         3         1         2
    ## 4         3         1         2         3         2         1         3
    ## 5         3         3         2         3         2         3         1
    ## 6         3         3         1         2         2         1         3
    ##   brief_q15 brief_q16 brief_q17 brief_q18 brief_q19 brief_q20 brief_q21
    ## 1         2         2         2         2         3         2         2
    ## 2         2         2         2         3         1         2         1
    ## 3         3         2         1         3         2         1         1
    ## 4         2         2         1         1         1         1         3
    ## 5         2         1         1         2         1         3         1
    ## 6         1         2         1         2         1         1         3
    ##   brief_q22 brief_q23 brief_q24 brief_q25 brief_q26 brief_q27 brief_q28
    ## 1         1         3         3         1         1         1         2
    ## 2         3         1         2         2         1         3         1
    ## 3         3         3         1         1         2         3         2
    ## 4         3         3         2         3         3         2         1
    ## 5         3         2         2         1         2         2         2
    ## 6         3         3         1         1         1         3         3
    ##   brief_q29 brief_q30 brief_q31 brief_q32 brief_q33 brief_q34 brief_q35
    ## 1         2         2         2         3         3         2         2
    ## 2         2         3         2         3         3         1         2
    ## 3         3         3         1         3         3         3         1
    ## 4         1         2         2         1         1         1         3
    ## 5         1         1         3         1         3         1         3
    ## 6         1         2         3         1         3         2         2
    ##   brief_q36 brief_q37 brief_q38 brief_q39 brief_q40 brief_q41 brief_q42
    ## 1         3         2         1         1         1         2         1
    ## 2         1         2         3         3         3         1         3
    ## 3         3         2         3         3         3         1         2
    ## 4         3         3         3         3         2         2         1
    ## 5         2         2         3         2         1         2         1
    ## 6         2         2         1         3         2         3         1
    ##   brief_q43 brief_q44 brief_q45 brief_q46 brief_q47 brief_q48 brief_q49
    ## 1         3         3         2         1         3         1         2
    ## 2         3         3         2         2         2         1         3
    ## 3         1         1         2         3         2         2         1
    ## 4         2         1         1         2         3         1         2
    ## 5         3         3         1         2         1         2         1
    ## 6         2         3         3         3         2         2         3
    ##   brief_q50 brief_q51 brief_q52 brief_q53 brief_q54 brief_q55 brief_q56
    ## 1         2         1         1         2         1         2         1
    ## 2         2         2         1         2         2         2         1
    ## 3         1         2         3         1         1         1         2
    ## 4         3         3         2         1         1         3         1
    ## 5         3         3         3         1         1         3         2
    ## 6         2         2         1         2         2         3         1
    ##   brief_q57 brief_q58 brief_q59 brief_q60 brief_q61 brief_q62 brief_q63
    ## 1         3         2         3         2         3         1         3
    ## 2         3         3         2         3         2         2         1
    ## 3         1         2         1         3         2         2         1
    ## 4         3         3         2         2         3         2         2
    ## 5         2         1         2         2         1         2         2
    ## 6         3         3         3         3         3         1         2
    ##   brief_q64 brief_q65 brief_q66 brief_q67 brief_q68 brief_q69 brief_q70
    ## 1         3         1         1         2         3         1         3
    ## 2         3         2         3         2         1         1         2
    ## 3         3         1         3         2         1         1         2
    ## 4         1         1         3         1         1         3         3
    ## 5         2         2         3         2         3         1         2
    ## 6         2         3         1         3         3         2         2
    ##   brief_q71 brief_q72 brief_q73 brief_q74 brief_q75
    ## 1         2         2         3         3         3
    ## 2         3         1         2         1         3
    ## 3         3         1         1         2         2
    ## 4         1         3         1         1         2
    ## 5         2         3         3         3         2
    ## 6         2         2         3         3         1

## Data preparation

Age and define the 75

```r
processed_data <- dummy_data %>%
  generate_age_group_10(age = age)
```

## Compute raw scores

for all scales and indices.

```r
processed_data <- processed_data %>%
  compute_brief_scores_raw() %>%
  select(
    contains("score_raw"),
    everything()
    )

head(processed_data)
```

    ##   score_raw_inhibit score_raw_shift score_raw_emot_cont score_raw_self_mon
    ## 1                19              15                  20                 14
    ## 2                18              15                  19                 12
    ## 3                15              13                  18                 12
    ## 4                18              12                  21                 14
    ## 5                16              13                  19                 14
    ## 6                18              16                  20                 12
    ##   score_raw_initiate score_raw_wm score_raw_plan_org score_raw_task_mon
    ## 1                 12           14                 20                 13
    ## 2                 16           13                 21                 12
    ## 3                 13           15                 23                 12
    ## 4                 16           15                 20                 11
    ## 5                 13           17                 18                 14
    ## 6                 17           14                 21                 11
    ##   score_raw_org_of_mat score_raw_bri score_raw_mi score_raw_gec age brief_q01
    ## 1                   13            68           72           140  68         1
    ## 2                   20            64           82           146  43         3
    ## 3                   16            58           79           137  74         1
    ## 4                   15            65           77           142  66         3
    ## 5                   16            62           78           140  90         1
    ## 6                   21            66           84           150  37         1
    ##   brief_q02 brief_q03 brief_q04 brief_q05 brief_q06 brief_q07 brief_q08
    ## 1         2         1         1         2         1         1         3
    ## 2         2         3         1         3         1         3         2
    ## 3         2         2         2         2         3         1         2
    ## 4         2         2         1         3         3         3         3
    ## 5         3         3         1         2         3         1         3
    ## 6         3         3         1         2         3         2         3
    ##   brief_q09 brief_q10 brief_q11 brief_q12 brief_q13 brief_q14 brief_q15
    ## 1         3         3         3         3         1         1         2
    ## 2         3         3         3         1         2         2         2
    ## 3         3         2         3         3         1         2         3
    ## 4         1         2         3         2         1         3         2
    ## 5         3         2         3         2         3         1         2
    ## 6         3         1         2         2         1         3         1
    ##   brief_q16 brief_q17 brief_q18 brief_q19 brief_q20 brief_q21 brief_q22
    ## 1         2         2         2         3         2         2         1
    ## 2         2         2         3         1         2         1         3
    ## 3         2         1         3         2         1         1         3
    ## 4         2         1         1         1         1         3         3
    ## 5         1         1         2         1         3         1         3
    ## 6         2         1         2         1         1         3         3
    ##   brief_q23 brief_q24 brief_q25 brief_q26 brief_q27 brief_q28 brief_q29
    ## 1         3         3         1         1         1         2         2
    ## 2         1         2         2         1         3         1         2
    ## 3         3         1         1         2         3         2         3
    ## 4         3         2         3         3         2         1         1
    ## 5         2         2         1         2         2         2         1
    ## 6         3         1         1         1         3         3         1
    ##   brief_q30 brief_q31 brief_q32 brief_q33 brief_q34 brief_q35 brief_q36
    ## 1         2         2         3         3         2         2         3
    ## 2         3         2         3         3         1         2         1
    ## 3         3         1         3         3         3         1         3
    ## 4         2         2         1         1         1         3         3
    ## 5         1         3         1         3         1         3         2
    ## 6         2         3         1         3         2         2         2
    ##   brief_q37 brief_q38 brief_q39 brief_q40 brief_q41 brief_q42 brief_q43
    ## 1         2         1         1         1         2         1         3
    ## 2         2         3         3         3         1         3         3
    ## 3         2         3         3         3         1         2         1
    ## 4         3         3         3         2         2         1         2
    ## 5         2         3         2         1         2         1         3
    ## 6         2         1         3         2         3         1         2
    ##   brief_q44 brief_q45 brief_q46 brief_q47 brief_q48 brief_q49 brief_q50
    ## 1         3         2         1         3         1         2         2
    ## 2         3         2         2         2         1         3         2
    ## 3         1         2         3         2         2         1         1
    ## 4         1         1         2         3         1         2         3
    ## 5         3         1         2         1         2         1         3
    ## 6         3         3         3         2         2         3         2
    ##   brief_q51 brief_q52 brief_q53 brief_q54 brief_q55 brief_q56 brief_q57
    ## 1         1         1         2         1         2         1         3
    ## 2         2         1         2         2         2         1         3
    ## 3         2         3         1         1         1         2         1
    ## 4         3         2         1         1         3         1         3
    ## 5         3         3         1         1         3         2         2
    ## 6         2         1         2         2         3         1         3
    ##   brief_q58 brief_q59 brief_q60 brief_q61 brief_q62 brief_q63 brief_q64
    ## 1         2         3         2         3         1         3         3
    ## 2         3         2         3         2         2         1         3
    ## 3         2         1         3         2         2         1         3
    ## 4         3         2         2         3         2         2         1
    ## 5         1         2         2         1         2         2         2
    ## 6         3         3         3         3         1         2         2
    ##   brief_q65 brief_q66 brief_q67 brief_q68 brief_q69 brief_q70 brief_q71
    ## 1         1         1         2         3         1         3         2
    ## 2         2         3         2         1         1         2         3
    ## 3         1         3         2         1         1         2         3
    ## 4         1         3         1         1         3         3         1
    ## 5         2         3         2         3         1         2         2
    ## 6         3         1         3         3         2         2         2
    ##   brief_q72 brief_q73 brief_q74 brief_q75 age10
    ## 1         2         3         3         3 60-69
    ## 2         1         2         1         3 40-49
    ## 3         1         1         2         2 70-79
    ## 4         3         1         1         2 60-69
    ## 5         3         3         3         2 80-90
    ## 6         2         3         3         1 30-39

## Compute T-scores

```r
processed_data <- processed_data %>%
  compute_brief_scores_t() %>%
  select(
    contains("score_t"),
    everything()
    )

head(processed_data)
```

    ##   score_t_gec score_t_mi score_t_bri score_t_inhibit score_t_shift
    ## 1          74         65          81              78            82
    ## 2          73         70          72              73            79
    ## 3          74         73          72              66            74
    ## 4          75         70          78              75            68
    ## 5          78         75          79              70            75
    ## 6          73         70          73              71            83
    ##   score_t_emot_cont score_t_self_mon score_t_initiate score_t_wm
    ## 1                68               75               54         63
    ## 2                61               64               66         58
    ## 3                65               66               59         69
    ## 4                71               75               69         67
    ## 5                71               77               60         79
    ## 6                62               64               68         60
    ##   score_t_plan_org score_t_task_mon score_t_org_of_mat score_raw_inhibit
    ## 1               71               73                 55                19
    ## 2               71               66                 74                18
    ## 3               83               68                 68                15
    ## 4               71               63                 62                18
    ## 5               68               78                 70                16
    ## 6               69               60                 74                18
    ##   score_raw_shift score_raw_emot_cont score_raw_self_mon score_raw_initiate
    ## 1              15                  20                 14                 12
    ## 2              15                  19                 12                 16
    ## 3              13                  18                 12                 13
    ## 4              12                  21                 14                 16
    ## 5              13                  19                 14                 13
    ## 6              16                  20                 12                 17
    ##   score_raw_wm score_raw_plan_org score_raw_task_mon score_raw_org_of_mat
    ## 1           14                 20                 13                   13
    ## 2           13                 21                 12                   20
    ## 3           15                 23                 12                   16
    ## 4           15                 20                 11                   15
    ## 5           17                 18                 14                   16
    ## 6           14                 21                 11                   21
    ##   score_raw_bri score_raw_mi score_raw_gec age brief_q01 brief_q02 brief_q03
    ## 1            68           72           140  68         1         2         1
    ## 2            64           82           146  43         3         2         3
    ## 3            58           79           137  74         1         2         2
    ## 4            65           77           142  66         3         2         2
    ## 5            62           78           140  90         1         3         3
    ## 6            66           84           150  37         1         3         3
    ##   brief_q04 brief_q05 brief_q06 brief_q07 brief_q08 brief_q09 brief_q10
    ## 1         1         2         1         1         3         3         3
    ## 2         1         3         1         3         2         3         3
    ## 3         2         2         3         1         2         3         2
    ## 4         1         3         3         3         3         1         2
    ## 5         1         2         3         1         3         3         2
    ## 6         1         2         3         2         3         3         1
    ##   brief_q11 brief_q12 brief_q13 brief_q14 brief_q15 brief_q16 brief_q17
    ## 1         3         3         1         1         2         2         2
    ## 2         3         1         2         2         2         2         2
    ## 3         3         3         1         2         3         2         1
    ## 4         3         2         1         3         2         2         1
    ## 5         3         2         3         1         2         1         1
    ## 6         2         2         1         3         1         2         1
    ##   brief_q18 brief_q19 brief_q20 brief_q21 brief_q22 brief_q23 brief_q24
    ## 1         2         3         2         2         1         3         3
    ## 2         3         1         2         1         3         1         2
    ## 3         3         2         1         1         3         3         1
    ## 4         1         1         1         3         3         3         2
    ## 5         2         1         3         1         3         2         2
    ## 6         2         1         1         3         3         3         1
    ##   brief_q25 brief_q26 brief_q27 brief_q28 brief_q29 brief_q30 brief_q31
    ## 1         1         1         1         2         2         2         2
    ## 2         2         1         3         1         2         3         2
    ## 3         1         2         3         2         3         3         1
    ## 4         3         3         2         1         1         2         2
    ## 5         1         2         2         2         1         1         3
    ## 6         1         1         3         3         1         2         3
    ##   brief_q32 brief_q33 brief_q34 brief_q35 brief_q36 brief_q37 brief_q38
    ## 1         3         3         2         2         3         2         1
    ## 2         3         3         1         2         1         2         3
    ## 3         3         3         3         1         3         2         3
    ## 4         1         1         1         3         3         3         3
    ## 5         1         3         1         3         2         2         3
    ## 6         1         3         2         2         2         2         1
    ##   brief_q39 brief_q40 brief_q41 brief_q42 brief_q43 brief_q44 brief_q45
    ## 1         1         1         2         1         3         3         2
    ## 2         3         3         1         3         3         3         2
    ## 3         3         3         1         2         1         1         2
    ## 4         3         2         2         1         2         1         1
    ## 5         2         1         2         1         3         3         1
    ## 6         3         2         3         1         2         3         3
    ##   brief_q46 brief_q47 brief_q48 brief_q49 brief_q50 brief_q51 brief_q52
    ## 1         1         3         1         2         2         1         1
    ## 2         2         2         1         3         2         2         1
    ## 3         3         2         2         1         1         2         3
    ## 4         2         3         1         2         3         3         2
    ## 5         2         1         2         1         3         3         3
    ## 6         3         2         2         3         2         2         1
    ##   brief_q53 brief_q54 brief_q55 brief_q56 brief_q57 brief_q58 brief_q59
    ## 1         2         1         2         1         3         2         3
    ## 2         2         2         2         1         3         3         2
    ## 3         1         1         1         2         1         2         1
    ## 4         1         1         3         1         3         3         2
    ## 5         1         1         3         2         2         1         2
    ## 6         2         2         3         1         3         3         3
    ##   brief_q60 brief_q61 brief_q62 brief_q63 brief_q64 brief_q65 brief_q66
    ## 1         2         3         1         3         3         1         1
    ## 2         3         2         2         1         3         2         3
    ## 3         3         2         2         1         3         1         3
    ## 4         2         3         2         2         1         1         3
    ## 5         2         1         2         2         2         2         3
    ## 6         3         3         1         2         2         3         1
    ##   brief_q67 brief_q68 brief_q69 brief_q70 brief_q71 brief_q72 brief_q73
    ## 1         2         3         1         3         2         2         3
    ## 2         2         1         1         2         3         1         2
    ## 3         2         1         1         2         3         1         1
    ## 4         1         1         3         3         1         3         1
    ## 5         2         3         1         2         2         3         3
    ## 6         3         3         2         2         2         2         3
    ##   brief_q74 brief_q75 age10
    ## 1         3         3 60-69
    ## 2         1         3 40-49
    ## 3         2         2 70-79
    ## 4         1         2 60-69
    ## 5         3         2 80-90
    ## 6         3         1 30-39

## Compute cutoff-scores

```r
processed_data <- processed_data %>%
  compute_brief_scores_cutoff() %>%
  select(
    contains("_cutoff"),
    everything()
    )

head(processed_data)
```

    ##   score_t_gec_cutoff_65 score_t_mi_cutoff_65 score_t_bri_cutoff_65
    ## 1                     1                    1                     1
    ## 2                     1                    1                     1
    ## 3                     1                    1                     1
    ## 4                     1                    1                     1
    ## 5                     1                    1                     1
    ## 6                     1                    1                     1
    ##   score_t_inhibit_cutoff_65 score_t_shift_cutoff_65 score_t_emot_cont_cutoff_65
    ## 1                         1                       1                           1
    ## 2                         1                       1                           0
    ## 3                         1                       1                           1
    ## 4                         1                       1                           1
    ## 5                         1                       1                           1
    ## 6                         1                       1                           0
    ##   score_t_self_mon_cutoff_65 score_t_initiate_cutoff_65 score_t_wm_cutoff_65
    ## 1                          1                          0                    0
    ## 2                          0                          1                    0
    ## 3                          1                          0                    1
    ## 4                          1                          1                    1
    ## 5                          1                          0                    1
    ## 6                          0                          1                    0
    ##   score_t_plan_org_cutoff_65 score_t_task_mon_cutoff_65
    ## 1                          1                          1
    ## 2                          1                          1
    ## 3                          1                          1
    ## 4                          1                          0
    ## 5                          1                          1
    ## 6                          1                          0
    ##   score_t_org_of_mat_cutoff_65 score_t_gec score_t_mi score_t_bri
    ## 1                            0          74         65          81
    ## 2                            1          73         70          72
    ## 3                            1          74         73          72
    ## 4                            0          75         70          78
    ## 5                            1          78         75          79
    ## 6                            1          73         70          73
    ##   score_t_inhibit score_t_shift score_t_emot_cont score_t_self_mon
    ## 1              78            82                68               75
    ## 2              73            79                61               64
    ## 3              66            74                65               66
    ## 4              75            68                71               75
    ## 5              70            75                71               77
    ## 6              71            83                62               64
    ##   score_t_initiate score_t_wm score_t_plan_org score_t_task_mon
    ## 1               54         63               71               73
    ## 2               66         58               71               66
    ## 3               59         69               83               68
    ## 4               69         67               71               63
    ## 5               60         79               68               78
    ## 6               68         60               69               60
    ##   score_t_org_of_mat score_raw_inhibit score_raw_shift score_raw_emot_cont
    ## 1                 55                19              15                  20
    ## 2                 74                18              15                  19
    ## 3                 68                15              13                  18
    ## 4                 62                18              12                  21
    ## 5                 70                16              13                  19
    ## 6                 74                18              16                  20
    ##   score_raw_self_mon score_raw_initiate score_raw_wm score_raw_plan_org
    ## 1                 14                 12           14                 20
    ## 2                 12                 16           13                 21
    ## 3                 12                 13           15                 23
    ## 4                 14                 16           15                 20
    ## 5                 14                 13           17                 18
    ## 6                 12                 17           14                 21
    ##   score_raw_task_mon score_raw_org_of_mat score_raw_bri score_raw_mi
    ## 1                 13                   13            68           72
    ## 2                 12                   20            64           82
    ## 3                 12                   16            58           79
    ## 4                 11                   15            65           77
    ## 5                 14                   16            62           78
    ## 6                 11                   21            66           84
    ##   score_raw_gec age brief_q01 brief_q02 brief_q03 brief_q04 brief_q05 brief_q06
    ## 1           140  68         1         2         1         1         2         1
    ## 2           146  43         3         2         3         1         3         1
    ## 3           137  74         1         2         2         2         2         3
    ## 4           142  66         3         2         2         1         3         3
    ## 5           140  90         1         3         3         1         2         3
    ## 6           150  37         1         3         3         1         2         3
    ##   brief_q07 brief_q08 brief_q09 brief_q10 brief_q11 brief_q12 brief_q13
    ## 1         1         3         3         3         3         3         1
    ## 2         3         2         3         3         3         1         2
    ## 3         1         2         3         2         3         3         1
    ## 4         3         3         1         2         3         2         1
    ## 5         1         3         3         2         3         2         3
    ## 6         2         3         3         1         2         2         1
    ##   brief_q14 brief_q15 brief_q16 brief_q17 brief_q18 brief_q19 brief_q20
    ## 1         1         2         2         2         2         3         2
    ## 2         2         2         2         2         3         1         2
    ## 3         2         3         2         1         3         2         1
    ## 4         3         2         2         1         1         1         1
    ## 5         1         2         1         1         2         1         3
    ## 6         3         1         2         1         2         1         1
    ##   brief_q21 brief_q22 brief_q23 brief_q24 brief_q25 brief_q26 brief_q27
    ## 1         2         1         3         3         1         1         1
    ## 2         1         3         1         2         2         1         3
    ## 3         1         3         3         1         1         2         3
    ## 4         3         3         3         2         3         3         2
    ## 5         1         3         2         2         1         2         2
    ## 6         3         3         3         1         1         1         3
    ##   brief_q28 brief_q29 brief_q30 brief_q31 brief_q32 brief_q33 brief_q34
    ## 1         2         2         2         2         3         3         2
    ## 2         1         2         3         2         3         3         1
    ## 3         2         3         3         1         3         3         3
    ## 4         1         1         2         2         1         1         1
    ## 5         2         1         1         3         1         3         1
    ## 6         3         1         2         3         1         3         2
    ##   brief_q35 brief_q36 brief_q37 brief_q38 brief_q39 brief_q40 brief_q41
    ## 1         2         3         2         1         1         1         2
    ## 2         2         1         2         3         3         3         1
    ## 3         1         3         2         3         3         3         1
    ## 4         3         3         3         3         3         2         2
    ## 5         3         2         2         3         2         1         2
    ## 6         2         2         2         1         3         2         3
    ##   brief_q42 brief_q43 brief_q44 brief_q45 brief_q46 brief_q47 brief_q48
    ## 1         1         3         3         2         1         3         1
    ## 2         3         3         3         2         2         2         1
    ## 3         2         1         1         2         3         2         2
    ## 4         1         2         1         1         2         3         1
    ## 5         1         3         3         1         2         1         2
    ## 6         1         2         3         3         3         2         2
    ##   brief_q49 brief_q50 brief_q51 brief_q52 brief_q53 brief_q54 brief_q55
    ## 1         2         2         1         1         2         1         2
    ## 2         3         2         2         1         2         2         2
    ## 3         1         1         2         3         1         1         1
    ## 4         2         3         3         2         1         1         3
    ## 5         1         3         3         3         1         1         3
    ## 6         3         2         2         1         2         2         3
    ##   brief_q56 brief_q57 brief_q58 brief_q59 brief_q60 brief_q61 brief_q62
    ## 1         1         3         2         3         2         3         1
    ## 2         1         3         3         2         3         2         2
    ## 3         2         1         2         1         3         2         2
    ## 4         1         3         3         2         2         3         2
    ## 5         2         2         1         2         2         1         2
    ## 6         1         3         3         3         3         3         1
    ##   brief_q63 brief_q64 brief_q65 brief_q66 brief_q67 brief_q68 brief_q69
    ## 1         3         3         1         1         2         3         1
    ## 2         1         3         2         3         2         1         1
    ## 3         1         3         1         3         2         1         1
    ## 4         2         1         1         3         1         1         3
    ## 5         2         2         2         3         2         3         1
    ## 6         2         2         3         1         3         3         2
    ##   brief_q70 brief_q71 brief_q72 brief_q73 brief_q74 brief_q75 age10
    ## 1         3         2         2         3         3         3 60-69
    ## 2         2         3         1         2         1         3 40-49
    ## 3         2         3         1         1         2         2 70-79
    ## 4         3         1         3         1         1         2 60-69
    ## 5         2         2         3         3         3         2 80-90
    ## 6         2         2         2         3         3         1 30-39

## Compute validity scores

```r
processed_data <- processed_data %>%
  compute_brief_validity_scores() %>%
  select(
    contains("validity_"),
    everything()
    )

head(processed_data)
```

    ##   brief_validity_negativity_exclude brief_validity_infreq_exclude
    ## 1                                 0                             1
    ## 2                                 0                             1
    ## 3                                 1                             0
    ## 4                                 1                             0
    ## 5                                 0                             0
    ## 6                                 0                             0
    ##   brief_validity_exclude_total score_t_gec_cutoff_65 score_t_mi_cutoff_65
    ## 1                            0                     1                    1
    ## 2                            1                     1                    1
    ## 3                            1                     1                    1
    ## 4                            1                     1                    1
    ## 5                            0                     1                    1
    ## 6                            0                     1                    1
    ##   score_t_bri_cutoff_65 score_t_inhibit_cutoff_65 score_t_shift_cutoff_65
    ## 1                     1                         1                       1
    ## 2                     1                         1                       1
    ## 3                     1                         1                       1
    ## 4                     1                         1                       1
    ## 5                     1                         1                       1
    ## 6                     1                         1                       1
    ##   score_t_emot_cont_cutoff_65 score_t_self_mon_cutoff_65
    ## 1                           1                          1
    ## 2                           0                          0
    ## 3                           1                          1
    ## 4                           1                          1
    ## 5                           1                          1
    ## 6                           0                          0
    ##   score_t_initiate_cutoff_65 score_t_wm_cutoff_65 score_t_plan_org_cutoff_65
    ## 1                          0                    0                          1
    ## 2                          1                    0                          1
    ## 3                          0                    1                          1
    ## 4                          1                    1                          1
    ## 5                          0                    1                          1
    ## 6                          1                    0                          1
    ##   score_t_task_mon_cutoff_65 score_t_org_of_mat_cutoff_65 score_t_gec
    ## 1                          1                            0          74
    ## 2                          1                            1          73
    ## 3                          1                            1          74
    ## 4                          0                            0          75
    ## 5                          1                            1          78
    ## 6                          0                            1          73
    ##   score_t_mi score_t_bri score_t_inhibit score_t_shift score_t_emot_cont
    ## 1         65          81              78            82                68
    ## 2         70          72              73            79                61
    ## 3         73          72              66            74                65
    ## 4         70          78              75            68                71
    ## 5         75          79              70            75                71
    ## 6         70          73              71            83                62
    ##   score_t_self_mon score_t_initiate score_t_wm score_t_plan_org
    ## 1               75               54         63               71
    ## 2               64               66         58               71
    ## 3               66               59         69               83
    ## 4               75               69         67               71
    ## 5               77               60         79               68
    ## 6               64               68         60               69
    ##   score_t_task_mon score_t_org_of_mat score_raw_inhibit score_raw_shift
    ## 1               73                 55                19              15
    ## 2               66                 74                18              15
    ## 3               68                 68                15              13
    ## 4               63                 62                18              12
    ## 5               78                 70                16              13
    ## 6               60                 74                18              16
    ##   score_raw_emot_cont score_raw_self_mon score_raw_initiate score_raw_wm
    ## 1                  20                 14                 12           14
    ## 2                  19                 12                 16           13
    ## 3                  18                 12                 13           15
    ## 4                  21                 14                 16           15
    ## 5                  19                 14                 13           17
    ## 6                  20                 12                 17           14
    ##   score_raw_plan_org score_raw_task_mon score_raw_org_of_mat score_raw_bri
    ## 1                 20                 13                   13            68
    ## 2                 21                 12                   20            64
    ## 3                 23                 12                   16            58
    ## 4                 20                 11                   15            65
    ## 5                 18                 14                   16            62
    ## 6                 21                 11                   21            66
    ##   score_raw_mi score_raw_gec age brief_q01 brief_q02 brief_q03 brief_q04
    ## 1           72           140  68         1         2         1         1
    ## 2           82           146  43         3         2         3         1
    ## 3           79           137  74         1         2         2         2
    ## 4           77           142  66         3         2         2         1
    ## 5           78           140  90         1         3         3         1
    ## 6           84           150  37         1         3         3         1
    ##   brief_q05 brief_q06 brief_q07 brief_q08 brief_q09 brief_q10 brief_q11
    ## 1         2         1         1         3         3         3         3
    ## 2         3         1         3         2         3         3         3
    ## 3         2         3         1         2         3         2         3
    ## 4         3         3         3         3         1         2         3
    ## 5         2         3         1         3         3         2         3
    ## 6         2         3         2         3         3         1         2
    ##   brief_q12 brief_q13 brief_q14 brief_q15 brief_q16 brief_q17 brief_q18
    ## 1         3         1         1         2         2         2         2
    ## 2         1         2         2         2         2         2         3
    ## 3         3         1         2         3         2         1         3
    ## 4         2         1         3         2         2         1         1
    ## 5         2         3         1         2         1         1         2
    ## 6         2         1         3         1         2         1         2
    ##   brief_q19 brief_q20 brief_q21 brief_q22 brief_q23 brief_q24 brief_q25
    ## 1         3         2         2         1         3         3         1
    ## 2         1         2         1         3         1         2         2
    ## 3         2         1         1         3         3         1         1
    ## 4         1         1         3         3         3         2         3
    ## 5         1         3         1         3         2         2         1
    ## 6         1         1         3         3         3         1         1
    ##   brief_q26 brief_q27 brief_q28 brief_q29 brief_q30 brief_q31 brief_q32
    ## 1         1         1         2         2         2         2         3
    ## 2         1         3         1         2         3         2         3
    ## 3         2         3         2         3         3         1         3
    ## 4         3         2         1         1         2         2         1
    ## 5         2         2         2         1         1         3         1
    ## 6         1         3         3         1         2         3         1
    ##   brief_q33 brief_q34 brief_q35 brief_q36 brief_q37 brief_q38 brief_q39
    ## 1         3         2         2         3         2         1         1
    ## 2         3         1         2         1         2         3         3
    ## 3         3         3         1         3         2         3         3
    ## 4         1         1         3         3         3         3         3
    ## 5         3         1         3         2         2         3         2
    ## 6         3         2         2         2         2         1         3
    ##   brief_q40 brief_q41 brief_q42 brief_q43 brief_q44 brief_q45 brief_q46
    ## 1         1         2         1         3         3         2         1
    ## 2         3         1         3         3         3         2         2
    ## 3         3         1         2         1         1         2         3
    ## 4         2         2         1         2         1         1         2
    ## 5         1         2         1         3         3         1         2
    ## 6         2         3         1         2         3         3         3
    ##   brief_q47 brief_q48 brief_q49 brief_q50 brief_q51 brief_q52 brief_q53
    ## 1         3         1         2         2         1         1         2
    ## 2         2         1         3         2         2         1         2
    ## 3         2         2         1         1         2         3         1
    ## 4         3         1         2         3         3         2         1
    ## 5         1         2         1         3         3         3         1
    ## 6         2         2         3         2         2         1         2
    ##   brief_q54 brief_q55 brief_q56 brief_q57 brief_q58 brief_q59 brief_q60
    ## 1         1         2         1         3         2         3         2
    ## 2         2         2         1         3         3         2         3
    ## 3         1         1         2         1         2         1         3
    ## 4         1         3         1         3         3         2         2
    ## 5         1         3         2         2         1         2         2
    ## 6         2         3         1         3         3         3         3
    ##   brief_q61 brief_q62 brief_q63 brief_q64 brief_q65 brief_q66 brief_q67
    ## 1         3         1         3         3         1         1         2
    ## 2         2         2         1         3         2         3         2
    ## 3         2         2         1         3         1         3         2
    ## 4         3         2         2         1         1         3         1
    ## 5         1         2         2         2         2         3         2
    ## 6         3         1         2         2         3         1         3
    ##   brief_q68 brief_q69 brief_q70 brief_q71 brief_q72 brief_q73 brief_q74
    ## 1         3         1         3         2         2         3         3
    ## 2         1         1         2         3         1         2         1
    ## 3         1         1         2         3         1         1         2
    ## 4         1         3         3         1         3         1         1
    ## 5         3         1         2         2         3         3         3
    ## 6         3         2         2         2         2         3         3
    ##   brief_q75 age10
    ## 1         3 60-69
    ## 2         3 40-49
    ## 3         2 70-79
    ## 4         2 60-69
    ## 5         2 80-90
    ## 6         1 30-39

## Complete workflow example

```r
analysis_data <- dummy_data %>%
  generate_age_group_10(age = age) %>%
  compute_brief_scores_raw() %>%
  compute_brief_scores_t() %>%
  compute_brief_scores_cutoff() %>%
  compute_brief_scores_cutoff(cutoff = 61.3) %>%
  compute_brief_validity_scores()
```
