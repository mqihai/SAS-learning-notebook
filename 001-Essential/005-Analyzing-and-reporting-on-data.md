## Title and Footnotes
1. add
```sas
TITLE(n) "title-text";
FOOTNOTE(n) "footnote-text";
```
2. turn off
```sas
TITLE;
FOOTNOTE;
ods noproctitle;
```
3. macro variables can be used in title-text

## Column labels
```sas
LABEL col-name="label-text";
```

1. proc print procedure can not dispaly the labels automatically, but we add LABEL option in the print statement to dispaly them instead of variable name temprorily.
2. If we take the same LABEL statement and put it in a DATA step, labels are assigned to the designated columns as permanent attributes in the descriptor portion of the table.

## segmenting reports

1. You can use the BY statement in a reporting procedure to segment a report based on the unique values of one or more columns. To do this, you must sort the data first using the same BY-column.

**Ex1:**
```sas
proc sort data=sashelp.cars 
          out=cars_sort;
    by Origin;
run;
 
proc freq data=cars_sort;
    by Origin;
    tables Type;
run;
```
**Ex2:**
```sas
proc sort data=sashelp.cars 
          out=cars_sort;
    by Origin;
run;
 
proc freq data=cars_sort;
    by Origin;
    tables Type;
run;
```
**Ex3:**
```sas
proc sort data=pg1.storm_final out=storm_sort;
    by BasinName descending MaxWindMPH;
    where MaxWindMPH > 156;
run;

title "Category 5 Storms";
proc print data=storm_sort label noobs;
    by BasinName;
    var Season Name MaxWindMPH MinPressure StartDate StormLength;
    label MaxWindMPH="Max Wind (MPH)"
          MinPressure="Min Pressure"
          StartDate="Start Date"
          StormLength="Length of Storm (days)";
run;
title;
```

## Creating Frequency Reports and Graphs
1. in simple frequency report, we can **add statements or options** to display different kinds of tables and graphs.

```sas
PROC FREQ DATA=input-table (options);
        TABLES col-name* (/ options);
RUN;
```

**Ex4:**
```sas
ods graphics on;
ods noproctitle;
title "Frequency Report for Basin and Storm Month";
proc freq data=pg1.storm_final order=freq nlevels;
    tables BasinName StartDate / 
           nocum plots=freqplot(orient=horizontal scale=percent);
    format StartDate monname.;
    label BasinName="Basin"
          StartDate="Storm Month";
run;
title;
ods proctitle; 
```
2. we can use * to make a **two-way** prequency report.
```sas
PROC FREQ DATA=input-table (options);
        TABLES col-name*col-name (/ options);
RUN;
```
**Ex5:**
```sas
proc freq data=pg1.storm_final;
    tables BasinName*StartDate;
    tables BasinName*StartDate / norow nocol nopercent;
    tables BasinName*StartDate / crosslist;
    tables BasinName*StartDate / list;
    format StartDate monname.;
    label BasinName="Basin"
          StartDate="Storm Month";
run;
```
3. use **proc sgplot** to display two-way frequency.

**Ex6:**
```sas
proc sgplot data=pg1.np_codelookup;
    where Type in ('National Historic Site', 'National Monument',
                   'National Park');
    hbar region / group=type seglabel
                  fillattrs=(transparency=0.5) dataskin=crisp;
    keylegend / opaque across=1 position=bottomright
                location=inside;
    xaxis grid;
run;
title;
```
##  Creating Summary Statistics Reports
1. Use Means Statements
```sas
PROC MEANS DATA=input-table <stat-list>;
        VAR col-name(s);
        CLASS col-name(s);
        WAYS n;
RUN;
```
**Ex7:**
```sas
proc means data=pg1.storm_final mean median min max maxdec=0;
    var MaxWindMPH;
    class BasinName StormType;
    ways 0 1 2;
run;
```
2.Creating an Output Summary Table
```sas
OUTPUT OUT=output-table statistic(col-name)=col-name / option(s);
```
