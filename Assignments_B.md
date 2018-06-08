# Assignments, Part B

Please email me your SAS code, the log and output.

## Fundamental Quarterly

For some of the assignments you need data from Compustat Fundamental Quarterly, and at the same time need to use industry code (SICH), which is on Fundamental Annual (but not on Fundamental Quarterly). For those questions you can adapt the following code which maches both datasets:

```SAS
%let wrds = wrds.wharton.upenn.edu 4016;options comamid = TCP remote=WRDS;
signon username=_prompt_;

rsubmit;
data myComp (keep = gvkey fyear datadate ni at sale sich );
set comp.funda;
/* require fyear to be within 2001-2015 */
if 2001 <=fyear <= 2015;
/* require assets, etc to be non-missing */
if cmiss (of at sale ceq ni) eq 0;
/* prevent double records */
if indfmt='INDL' and datafmt='STD' and popsrc='D' and consol='C' ;
run;

/* join with fundq */
proc sql;
  create table mycomp2 as
  select a.* , b.datadate as datadate_fundq, b.fqtr
  from myComp a left join comp.fundq b
  on a.gvkey = b.gvkey and a.fyear = b.fyearq
  and b.indfmt='INDL' and b.datafmt='STD' and b.popsrc='D' and b.consol='C';
quit;


proc download data=mycomp2 out=a_fundq;run;
endrsubmit;
```

## Assignment 5 - End of Quarter

For firms in Compustat Fundamental Annual and Quarterly determine the month of each quarter end (e.g. quarter ending in March is month 3); use the period 1980-2015; each firm has 4 quarter endings in a year. 

Give examples of a few  industries that commonly have quarter endings other than March, June, September and December? You can use SICH (historical SIC code), and look up the SIC code on [Wikipedia](https://en.wikipedia.org/wiki/Standard_Industrial_Classification).

Hint: the `month` function returns the month for a given date.


## Assignment 6 - Matching Yearly Data on Monthly Data

The following code creates two datasets: `returns` has monthly returns, and `yearly` has some yearly data:

```SAS

data returns;
  input @01 id       
  	@03 date  MMDDYY10.
        @14 return;
format date date9.;
datalines;
1 10/31/2013 0.01
1 11/30/2013 0.02
1 12/31/2013 0.03
1 01/31/2014 -0.01
1 02/28/2014 0.01
2 10/31/2013 -0.01
2 11/30/2013 0.02
2 12/31/2013 0.01
2 01/31/2014 -0.02
2 02/28/2014 -0.03
2 03/31/2014 0.02 
run;

data yearly;
  input @01 id        
  	@03 date  MMDDYY10.
        @14 equity;
format date date9.;
datalines;
1 12/31/2011 8
1 12/31/2012 10
1 12/31/2013 11
2 12/31/2012 30
2 12/31/2013 28
run;
```

Required: for each record in `returns` append the most recent yearly data (the date in `yearly` needs to be the most recent date, but needs to be before the date in `returns`).


## Assignment 7 - Large Firms for Industry-Quarters

Write SAS code that uses Compustat Quarterly (comp.fundq) and retrieves firm-quarters for 2004-2015 of firms with fiscal year end in December (fyr is 12). Additionally, there need to be at least 10 active firms in each 4-digit SICH industry-quarter for firms to be included. Determine industry-quarter median firm size (using sales), and flag each observation whether or not its sales are above/below the industry median. 



## Assignment 8 - Industry Leaders

Determine the industry leader for each 4-digit SICH industry-quarter. The industry leader has above-median sales, and is the first one to report earnings (you may use firms with a fiscal year-end in December, `fyr` is 12; variable `rdq` is the earnings reporting date). Also compute the number of days the firms report later relative to the industry leader. Use `proc rank` to create delay deciles, where the quickest filers are in decile 1 and the slowest filers in decile 10 (do this by year, as filing requirements changed over time). 

