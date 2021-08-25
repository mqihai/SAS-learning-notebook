## DATA Step
```sas    
DATA=output-table;
  SET input-table;
RUN;
```
1. data step processing: this step reads and creates one row each time and then loops back to make the second row.
2. filter data with ***where,keep,drop,format*** statement

**Ex1:**
```sas
libname pg1 "C:\EPG1V2-2\data";
libname output "C:\EPG1V2-2\output";
data output.storm_cat5;
  set  pg1.storm_summary;
  where MaxWindMPH>=156 and startdate>="01JAN2000"d;
  keep Season Basin Name Type MaxWindMPH;
run; 
```
**Ex2:**
```sas
%Let category=BIRD;
data &category;
  set pg1.np_species;
  where upcase(category)="&category";
  drop Abundance Seasonality Conservation_Status;
run;

proc freq data=&category;
tables record_status;
run;
```
3. create new column (with functions)
```sas
DATA output-table;
  SET input-table;
  new-column=expression;
  function(argument1, argument2, ...);
RUN;
```
Common Numeric Functions|
------------------------|
SUM (num1, num2, ...)|
MEAN (num1, num2, ...)|
MEDIAN (num1, num2, ...)|
RANGE (num1, num2, ...)|
MIN (num1, num2, ...)|
MAX (num1, num2, ...)|
N (num1, num2, ...)|
NMISS (num1, num2, ...)|

Character Function|What it Does
------------------|------------
UPCASE (char)LOWCASE(char)|Changes letters in a character string to uppercase or lowercase
PROPCASE (char, (delimiters)|Changes the first letter of each word to uppercase and other letters to lowercase
CATS (char1, char2, ...)|Concatenates character strings and removes leading and trailing blanks from each argument
SUBSTR (char, position, (length))|Returns a substring from a character string


Date Function|What it Does
-------------|------------
MONTH (SAS-date)|Returns a number from 1 through 12 that represents the month
YEAR (SAS-date)|Returns the four-digit year｜
DAY (SAS-date)|Returns a number from 1 through 31 that represents the day of the month
WEEKDAY (SAS-date)|Returns a number from 1 through 7 that represents the day of the week (Sunday=1)
QTR (SAS-date)|Returns a number from 1 through 4 that represents the quarter
TODAY ()|Returns the current date as a numeric SAS date value (no argument is required because the function reads the system clock)
MDY (month, day, year)|Returns a SAS date value from numeric month, day, and year values
YRDIF (startdate, enddate, 'AGE')|Calculates a precise age between two dates
	

**Ex3:**
```sas
data storm_length;
  set pg1.storm_summary;
  drop Hem_EW Hem_NS Lat Lon;
  stormlengh=enddate-startdate;
run;
```
**Ex4:**
```sas
data storm_wingavg;
  set pg1.storm_range;
  WindAvg=mean(wind1,wind2,wind3,wind4);
  WindRange=range(wind1,wind2,wind3,wind4);
  *WindRange=range(of wind1-wind4);
run;  
/*Note: you can use the shorthand notation OF col1 - coln to list a range of columns 
when the columns have the same name and end in a consecutive number.*/
```
**Ex5:**
```sas	
data pacific;
  set pg1.storm_summary;
  drop Type Hem_EW Hem_NS MinPressure Lat Lon;
  where substr(Basin,2,1)="P";
  *Add a WHERE statement that uses the SUBSTR function;
run;
```
**Ex6:**
```sas
data np_summary2;
  set pg1.np_summary;
  parktype=scan(parkname,-1);
  keep Reg Type ParkName ParkType;
run;
```
## Conditional processing with IF THEN
```sas
IF expression THEN statement;
<ELSE IF expression THEN statement;>
<ELSE IF expression THEN statement;>
ELSE statement;
```
1.sas tests all conditions in sequence, so in **Ex7**, the pressuregroup for minpressure missing row is 1, not treated as missing.
2.**Else If** can apply the expression to the first true condition, that means there is hierarchy.

**Ex7:**
```sas
data storm_new;
  set pg1.storm_summary;
  keep Season Name Basin MinPressure PressureGroup;
  if MinPressure=. then PressureGroup=. ;
  if MinPressure<=920 then PressureGroup=1;
  if MinPressure>920 then PressureGroup=0;
run;
```
3. Creating Character Columns with the LENGTH statement
```sas
LENGTH char-column $ length;
```
**Ex8:**
```sas
data storm_summary2;
  set pg1.storm_summary;
  length Ocean $ 8;
  keep Basin Season Name MaxWindMPH Ocean;
  Basin=upcase(Basin);
  OceanCode=substr(Basin,2,1);
  if OceanCode="I" then Ocean="Indian";
  else if OceanCode="A" then Ocean="Atlantic";
  else Ocean="Pacific";
run;
```
4. you can only have one executable statement following THEN, but can use THEN DO.
```sas
IF expression THEN DO;
    <executable statements> 
END; 
ELSE IF expression THEN DO;
    <executable statements>
END;
ELSE DO;
    <executable statements>
END;
```
**Ex9:**
```sas
data front rear;
  set sashelp.cars;
  if DriveTrain="Front" then do;
     DriveTrain="FWD";
     output front;
  end;    
  else if DriveTrain='Rear' then do;
          DriveTrain="RWD";
          output rear;
  end;    
run;
```
5. you can also use SELECT WHEN DO.
6. 
**Ex10:**
```sas
data parks monuments;
    set pg1.np_summary;
    where type in ('NM', 'NP');
    Campers=sum(OtherCamping, TentCampers, RVCampers,
                BackcountryCampers);
    format Campers comma17.;
    length ParkType $ 8;
    select (type);
        when ('NP') do;
            ParkType='Park';
            output parks;
		end;
		otherwise do;
            ParkType='Monument';
            output monuments;
		end;
    end;
    keep Reg ParkName DayVisits OtherLodging Campers ParkType;
run;
```

