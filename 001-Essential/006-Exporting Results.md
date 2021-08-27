## Exporting Data with proc export

```sas
PROC EXPORT DATA=input-table OUTFILE="output-file"
            DBMS =identifier REPLACE;
RUN;
```

## Exporting Data with libname
**Ex1:**
```sas
libname xlout xlsx "&outpath/southpacific.xlsx";
data xlout.South_Pacific;
    set pg1.storm_final;
    where Basin="SP";
run;
proc means data=pg1.storm_final noprint maxdec=1;
    where Basin="SP";
    var MaxWindKM;
    class Season;
    ways 1;
    output out=xlout.Season_Stats n=Count mean=AvgMaxWindKM max=StrongestWindKM;
run; 
```
## Exporting Data with ODS statements
```sas
ODS <destination> <destination-specifications>;
/* SAS code that produces output */
ODS <destination> CLOSE;


ODS EXCEL FILE="filename.xlsx" STYLE=style;
        OPTIONS(SHEET_NAME='label');
/* SAS code that produces output */
ODS EXCEL CLOSE;


ODS POWERPOINT FILE="filename.pptx" STYLE=style;
/* SAS code that produces output */
ODS POWERPOINT CLOSE;


ODS RTF FILE="filename.rtf" STARTPAGE=NO;
/* SAS code that produces output */
ODS RTF CLOSE;

ODS PDF FILE="filename.pdf" STYLE=style
                STARTPAGE=NO PDFTOC=n;
ODS PROCLABEL "label";

/* SAS code that produces output */

ODS PDF CLOSE;
```
**Ex2:**
```sas
ods csvall file="&outpath/cars.csv";
proc print data=sashelp.cars noobs;
    var Make Model Type MSRP MPG_City MPG_Highway;
    format MSRP dollar8.;
run;
ods csvall close;
```
**Ex3:**
```sas
ods pdf file="&outpath/wind.pdf" startpage=no style=journal pdftoc=1; 
ods noproctitle;

ods proclabel "Wind Statistics";
title "Wind Statistics by Basin";
proc means data=pg1.storm_final min mean median max maxdec=0;
    class BasinName;
    var MaxWindMPH;
run;

ods proclabel "Wind Distribution"; 
title "Distribution of Maximum Wind";
proc sgplot data=pg1.storm_final;
    histogram MaxWindMPH;
    density MaxWindMPH;
run;
title;

ods proctitle;
ods pdf close; 
```
