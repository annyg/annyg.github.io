---
layout: post
title: "SCImago Journal Rank and published papers"
author: "ANYthings"
date: "2024-04-25"
tags: code ggplot visualization tutorial kaggle
categories: datascience publication blog
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

## SCImago Journal Rank (SJR)

<https://libguides.daemen.edu/c.php?g=1239513&p=9072137>

SCImago Journal & Country Rank is a publicly available resource that
includes journals and scientific indicators developed from the
information provided in the Scopus database.

Journals are ranked by the SCImago Journal Rank (SJR), which attempts to
factor in subject field, quality, and reputation of a journal on
citations. Why use SJR?

    Free, public resource (no subscription needed)
    Ranks more journals than Journal Citation Reports (uses citation data from Scopus)
    Covers all disciplines
    Rankings are normalized to account for differences in citation behavior between disciplines

How SJR is Calculated: Transfer of Prestige

SCImago Journal Rank (SJR) measures the frequency with which content
published in a journal was cited in other journals during the three
previous years (compared to previous two years with Journal Impact
Factor). SCImago’s ranking system is rooted in the belief that citations
from prestigious journals are worth more than those from less
prestigious journals. SJR weights each incoming citation to a journal by
the SJR of the citing journal, with a citation from a source with a high
SJR counting for more than a citation from a source with a low SJR.

SCImago attempts to normalize their rankings to account for differences
in citation behavior between disciplines. The scoring scale brings
everything down to 1 for easy comparison. A journal with a SJR value \>
1.0 has above average citation potential and journal with a SJR value \<
1.0 has below average citation potential.

## Journal Impact Factor (JIF)

<https://libguides.daemen.edu/c.php?g=1239513&p=9072135> The most
well-known metric for assessing a journal is Journal Impact Factor or
Impact Factor. Created in 1969 as a means to help librarians make
collection development decisions, Journal Citation Reports (JCR)
publishes their annual results ranking journals in the areas of science,
technology, and social sciences.

Limitations:

    Subscription required to view rankings (Daemen Library does NOT subscribe to JCR)
    Limited to the citation data of journals indexed in Web of Science
    Not field-normalized, meaning that it does not take into account variations in citation behaviors across different disciplines. The Impact Factor should therefore only be used when making comparisons within the same discipline.
    Arts & Humanities journals are being added, but don't receive a Journal Impact Factor score (see Journal Citation Indicator section below)

How Impact Factor is Calculated

Journal Impact Factor (JIF) measures the frequency with which content
published in a journal was cited in other journals during the two
previous years.

Calculation of a 2020 IF of a journal:

a = the number of times “citable items” published in 2018 and 2019 were
cited by indexed journals during 2020. b = the total number of “citable
items” published in 2018 and 2019.

a / b = 2020 impact factor

Example:

Let’s say in the years 2018 and 2019 there were 100 citable items
published in a journal. In 2020, those 100 items were cited 400 times.

We divide the total citations (400) by the total items (100) to get a
2020 Journal Impact Factor of 4.0. In other words, items published in
the previous two years received an average of 4 citations during the JCR
year.

# Data sources

## SCIMAGO Journal Rank data

I downloaded the SCIMAGO Journal Rank data from the data science
community website, [Kaggle](www.kaggle.com). Here I found a post that
had all SCIMAGO Journal Rank data from 1999 until 2022. You can find the
data
[here](https://www.kaggle.com/datasets/alijalali4ai/scimagojr-scientific-journals-dataset).

## Publication data

I exported all my publications from Zotero as a bibtex file. Then I
converted my bibtex bibliography to a csv-file using the online [BibTeX
to CSV converter](https://www.bibtex.com/c/bibtex-to-csv-converter/)
provided by bibtex.com.

```r
library(tidyverse)
```

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

```r
library(hrbrthemes)
```

```r
list_of_files <- list.files(path = "raw_data/sjr/archive/ScimagoJR/",
                            recursive = TRUE,
                            pattern = "\\.csv$",
                            full.names = TRUE)

combined_data <- map_df(list_of_files, read.csv) %>%
  janitor::clean_names()
```

```r
combined_data <- combined_data %>%
  mutate(across(starts_with("total_docs") & matches("\\d$"), ~ case_when(
    !is.na(.) ~ as.integer(str_extract(cur_column(), "\\d+$")),
    TRUE ~ NA_integer_
  ), .names = "year_{.col}")) %>%
  unite("year", starts_with("year_total"), remove = TRUE, na.rm = TRUE)

pubs <- combined_data %>%
  select(title) %>%
  distinct()

combined_data <- combined_data %>%
  arrange(desc(sjr)) %>%
  group_by(year) %>%
  mutate(rank_tot = row_number()) %>%
  group_by(areas) %>%
  mutate(rank_area = row_number()) %>%
  ungroup() %>%
  select(-contains("total_docs")) %>%
  ungroup() %>%
  mutate(areas2 = str_split(areas, "; ", simplify = TRUE) %>% first())

head(combined_data)
```

    ## # A tibble: 6 × 23
    ##    rank sourceid title    type  issn    sjr sjr_best_quartile h_index total_refs
    ##   <int>    <dbl> <chr>    <chr> <chr> <dbl> <chr>               <int>      <int>
    ## 1     1    28773 Ca-A Ca… jour… 1542…  88.2 Q1                    198       2924
    ## 2     1    28773 Ca-A Ca… jour… 1542…  86.1 Q1                    198       4268
    ## 3     1    28773 Ca-A Ca… jour… 1542…  72.6 Q1                    198       2568
    ## 4     1    28773 Ca-A Ca… jour… 1542…  62.9 Q1                    198       3309
    ## 5     1    28773 Ca-A Ca… jour… 1542…  61.8 Q1                    198       3160
    ## 6     1    28773 Ca-A Ca… jour… 1542…  56.2 Q1                    198       3679
    ## # ℹ 14 more variables: total_cites_3years <int>, citable_docs_3years <int>,
    ## #   cites_doc_2years <dbl>, ref_doc <dbl>, country <chr>, region <chr>,
    ## #   publisher <chr>, coverage <chr>, categories <chr>, areas <chr>, year <chr>,
    ## #   rank_tot <int>, rank_area <int>, areas2 <chr[,10]>

```r
publications <- read_csv("raw_data/sjr/references.csv") %>%
  janitor::clean_names() %>%
  mutate(title = case_when(
    journal == "Sci Rep" ~ "Scientific Reports",
    journal == "BMC Infectious Diseases" ~ "BMC Infectious Diseases",
    journal == "Sci Data" ~ "Scientific data",
    journal == "BMJ" ~ "BMJ, The",
    journal == "Microbiome" ~ "Microbiome",
    journal == "Frontiers in Immunology" ~ "Frontiers in Immunology",
    journal == "International Journal of Infectious Diseases" ~ "International Journal of Infectious Diseases",
    journal == "J Neurol" ~ "Journal of Neurology",
    journal == "FEMS Microbiology Ecology" ~ "FEMS Microbiology Ecology",
    journal == "Brain Commun" ~ "Brain Communications",
    journal == "Indoor and Built Environment" ~ "Indoor and Built Environment",
    journal == "Influenza and Other Respiratory Viruses" ~ "Influenza and other Respiratory Viruses",
    journal == "JAMA Netw Open" ~ "JAMA network open",
    journal == "The Lancet" ~ "Lancet, The"
  )) %>%
  mutate(year = as.character(publication_year))
```

    ## Rows: 14 Columns: 22
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr  (17): Item type, Authors, Title, Journal, Pages, Date published, ISSN, ...
    ## dbl   (4): Publication year, Volume, Issue, PMID
    ## date  (1): Date accessed
    ##
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

```r
head(publications)
```

    ## # A tibble: 6 × 23
    ##   item_type       authors      title journal publication_year volume issue pages
    ##   <chr>           <chr>        <chr> <chr>              <dbl>  <dbl> <dbl> <chr>
    ## 1 Journal Article Nygaard AB,… Scie… Sci Rep             2020     10     1 3209
    ## 2 Journal Article Ellingjord-… BMC … BMC In…             2022     22     1 252
    ## 3 Journal Article Garcia-Gall… Scie… Sci Da…             2022      9     1 454
    ## 4 Journal Article Nygaard AB,… Micr… Microb…             2018      6     1 159
    ## 5 Journal Article Søraas A,Gr… Fron… Fronti…             2022     13    NA <NA>
    ## 6 Journal Article Brunvoll SH… BMJ,… BMJ                 2022    378    NA e071…
    ## # ℹ 15 more variables: date_published <chr>, date_accessed <date>, issn <chr>,
    ## #   issn_alt <chr>, ur_ls <chr>, doi <chr>, pmid <dbl>, pmc_id <chr>,
    ## #   abstract <chr>, keywords <chr>, notes <chr>, short_title <chr>,
    ## #   copyright <chr>, language <chr>, year <chr>

```r
any_stat <- combined_data %>%
  semi_join(publications, by = "title") %>%
  full_join(publications, by = c("year", "title")) %>%
  mutate(year = as.double(year)) %>%
  group_by(title) %>%
  complete(year = as.double(seq(min(year), max(year)))) %>%
  fill(sjr) %>%
  fill(areas) %>%
  fill(region) %>%
  fill(publisher) %>%
  mutate(year = as.double(year)) %>%
  ungroup()

any_label <- any_stat %>%
  group_by(title) %>%
  slice_max(order_by = year)

any_pubs <- any_stat %>%
  drop_na(item_type)
```

```r
fig_any <- any_stat %>%
  ggplot(aes(x = year, y = sjr, color = title)) +
  geom_hline(yintercept = 1, linetype = "longdash", color = "darkgrey") +
  geom_line(linewidth = 0.9, alpha = 0.9) +
  geom_point(data = any_pubs, size = 3, color = "grey", alpha = 0.7)  +
  ggsci::scale_color_d3("category20") +
  scale_x_continuous(limits = c(1999,2040)) +
  coord_cartesian(clip="off") +
  labs(
    x = "Year", y = "SCImago Journal Rank",
    title = "Published papers",
    subtitle="My published papers with data from SCImago",
    caption = "-Created by ANYthings-"
  ) +
  theme_modern_rc() +
  theme(
    legend.position = "none"
  )
fig_any +
  geom_text(data = any_label, aes(label=title), hjust = -0.2)
```

![](/assets/img/rmdposts/unnamed-chunk-6-1.png)<!-- -->

```r
fig_any_area <- fig_any +
  scale_x_continuous(limits = c(1999,2050)) +
  geom_text(data = any_label, aes(label=title), hjust = -0.1) +
  facet_wrap(.~areas, scales = "free_y")
```

    ## Scale for x is already present.
    ## Adding another scale for x, which will replace the existing scale.

```r
flush_ticks(fig_any_area)
```

    ## theme(axis.text.x=element_text(hjust=c(0, rep(0.5, 4), 1))) +
    ## theme(axis.text.y=element_text(vjust=c(0, rep(0.5, 3), 1)))

![](/assets/img/rmdposts/unnamed-chunk-7-1.png)<!-- -->

```r
fig_any_publ <- fig_any +
  scale_x_continuous(limits = c(1999,2050)) +
  geom_text(data = any_label, aes(label=title), hjust = -0.1) +
  facet_wrap(.~publisher, scales = "free_y")
```

    ## Scale for x is already present.
    ## Adding another scale for x, which will replace the existing scale.

```r
fig_any_publ
```

![](/assets/img/rmdposts/unnamed-chunk-8-1.png)<!-- -->

```r
fig_any_regi <- fig_any +
  scale_x_continuous(limits = c(1999,2050)) +
  geom_text(data = any_label, aes(label=title), hjust = -0.1) +
  facet_wrap(.~region, scales = "free_y")
```

    ## Scale for x is already present.
    ## Adding another scale for x, which will replace the existing scale.

```r
fig_any_regi
```

![](/assets/img/rmdposts/unnamed-chunk-9-1.png)<!-- -->
