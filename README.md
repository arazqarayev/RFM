# RFM
segmentation using RFM (recency, frequency and monetary) analysis.
RFM (recency, frequency, monetary) analysis is a behavior based technique used to segment customers by examining their transaction history such as:
•	how recently a customer has purchased (recency)
•	how often they purchase (frequency)
•	how much the customer spends (monetary)
It is based on the marketing axiom that 80% of your business comes from 20% of your customers. RFM analysis helps to identify customers who are more likely to respond to promotions by segmenting them into various categories.
To calculate the RFM score we need the following info for each customer:
•	a unique customer id
•	date of transaction/order
•	transaction/order amount








library(dplyr)
> setwd("C:\\Users\\TOSHIBA\\Desktop\\Data Codes\\Apriori")
> rfmders  <- read.csv('RFMler.csv')
> View(rfmders)
> ilk <- min(rfmders$order_date)
> print(ilk)
[1] "2001-10-29"
> ## ----rfm table-----------------------------------------------------------
> analysis_date <- lubridate::as_date("2001-10-29", tz = "UTC")
Warning message:
`tz` argument is ignored by `as_date()` 
> rfm_result    <- rfm_table_order(rfm_data_orders, customer_id, order_date, revenue, analysis_date)
> View(rfm_result)
> rfm_result
# A tibble: 995 × 9
   customer_id date_most_recent recency_days transaction_count amount recency_score
   <chr>       <date>                  <dbl>             <dbl>  <dbl>         <int>
 1 Abbey O'Re… 2006-06-09              -1684                 6    472             3
 2 Add Senger  2006-08-13              -1749                 3    340             4
 3 Aden Lesch… 2006-06-20              -1695                 4    405             3
 4 Admiral Se… 2006-08-21              -1757                 5    448             4
 5 Agness O'K… 2006-10-02              -1799                 9    843             5
 6 Aileen Bar… 2006-10-08              -1805                 9    763             5
 7 Ailene Her… 2006-03-25              -1608                 8    699             3
 8 Aiyanna Br… 2006-04-29              -1643                 4    157             3
 9 Ala Schmid… 2006-01-16              -1540                 3    363             2
10 Alannah Bo… 2005-04-21              -1270                 4    196             1
# ℹ 985 more rows
# ℹ 3 more variables: frequency_score <int>, monetary_score <int>, rfm_score <dbl>
# ℹ Use `print(n = ...)` to see more rows
> ## ----segments------------------------------------------------------------
> segment_names <- c("Champions", "Loyal Customers", "Potential Loyalist",
+                    "New Customers", "Promising", "Need Attention", "About To Sleep",
+                    "At Risk", "Can't Lose Them", "Hibernating", "Lost")
> recency_lower   <- c(4, 2, 3, 4, 3, 3, 2, 1, 1, 2, 1)
> recency_upper   <- c(5, 4, 5, 5, 4, 4, 3, 2, 1, 3, 1)
> frequency_lower <- c(4, 3, 1, 1, 1, 3, 1, 2, 4, 2, 1)
> frequency_upper <- c(5, 4, 3, 1, 1, 4, 2, 5, 5, 3, 1)
> monetary_lower  <- c(4, 4, 1, 1, 1, 3, 1, 2, 4, 2, 1)
> monetary_upper  <- c(5, 5, 3, 1, 1, 4, 2, 5, 5, 3, 1)  
> segments <- rfm_segment(rfm_result, segment_names, recency_lower, recency_upper,
+                         frequency_lower, frequency_upper, monetary_lower, monetary_upper)
> segments
# A tibble: 995 × 9
   customer_id        segment       rfm_score transaction_count recency_days amount
   <chr>              <chr>             <dbl>             <dbl>        <dbl>  <dbl>
 1 Abbey O'Reilly DVM Need Attenti…       343                 6        -1684    472
 2 Add Senger         Potential Lo…       412                 3        -1749    340
 3 Aden Lesch Sr.     Potential Lo…       323                 4        -1695    405
 4 Admiral Senger     Potential Lo…       433                 5        -1757    448
 5 Agness O'Keefe     Champions           555                 9        -1799    843
 6 Aileen Barton      Champions           555                 9        -1805    763
 7 Ailene Hermann     Others              355                 8        -1608    699
 8 Aiyanna Bruen PhD  Potential Lo…       321                 4        -1643    157
 9 Ala Schmidt DDS    About To Sle…       212                 3        -1540    363
10 Alannah Borer      Others              121                 4        -1270    196
# ℹ 985 more rows
# ℹ 3 more variables: recency_score <int>, frequency_score <int>,
#   monetary_score <int>
# ℹ Use `print(n = ...)` to see more rows
> ## ----tabulate segments---------------------------------------------------
> segments %>%
+   dplyr::count(segment) %>%
+   dplyr::arrange(dplyr::desc(n)) %>%
+   dplyr::rename(Segment = segment, Count = n)
# A tibble: 8 × 2
  Segment            Count
  <chr>              <int>
1 Potential Loyalist   277
2 Champions            158
3 At Risk              148
4 Others               128
5 Loyal Customers      126
6 Lost                  75
7 About To Sleep        65
8 Need Attention        18
> ## ----average recency-----------------------------------------------------
> rfm_plot_median_recency(segments)
> ## ----average frequency---------------------------------------------------
> rfm_plot_median_frequency(segments)
> ## ----average monetary value----------------------------------------------
> rfm_plot_median_monetary(segments)
> ## ----heatmap-------------------------------------------------------------
> rfm_heatmap(rfm_result)
> ## ----barchart------------------------------------------------------------
> rfm_bar_chart(rfm_result)

