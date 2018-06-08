# Assignments, Part A

Please email me your SAS code, the log and output.


## Assignment 1 - Lifetime Sales

Compute the aggregate life-time sales for each firm in Compustat Fundamental Annual (`comp.funda`). Then, aggregate by 4-digit SICH (historical industry code) to compute life-time sales for each industry. Drop industries with less than 20 firms in it.


## Assignment 2 - Missing SICH

For the observations in Funda, inspect by year how often SICH is missing. Write code to use the most recent past (non-missing) values to replace missing values in later years (for example, if SICH is missing for 2005, then use 2004 SICH; go back further in time if needed). For observations with no prior SICH available, use future values to backfill earlier years (for example, if values up to 2006 are missing, use SICH in 2006 for earlier years).


## Assignment 3 - Herfindahl Index

Compute the Herfindahl index for each 4-digit SIC industry-year. The formula for the index is on [Wikipedia](https://en.wikipedia.org/wiki/Herfindahl_index). 

Hint: first compute total industry sales


## Assignment 4 - Combining Rating Data

Consider the situation where you have ratings for firms, where the ratings are relevant for some period. However, sometimes there are multiple observations in the data where the rating does not change. You are required to combine these records.

Each row has a firm id, start year, end year and rating. When the rating stays the same, the period (start and end-year) may be combined. You may ignore possible gaps (when the beginning date of a next row is after the end date of the previous row).

> Note: There is more than one way to solve this problem. 

The following code creates a sample set:

```SAS
data ratingdata;
  input @01 id        1.
        @03 startyr   4.
        @08 endyr     4.
	@13 rating    $1.
	;
datalines;
1 2002 2004 A
1 2005 2007 A
1 2007 2009 B
1 2009 2010 A
2 2002 2004 A
3 2001 2003 B
3 2003 2004 B
3 2005 2007 B
run;

```

The output data should be:

id | rating_combined | start_combined | end_combined
------------ | ------------- | ------------ | ------------- 
1 | A | 2002 | 2007
1 | B | 2007 | 2009
1 | A | 2009 | 2010
2 | A | 2002 | 2004
3 | B | 2001 | 2007

