### SAS Step Processing
1. SAS runs DATA step with compilation phase and execution.
2. compilation: attributes like length must be set at first in DATA step.
3. compilation: drop statement does not affect the calculation using the variable, but affect the dispaly of the variable in the result table.
4. putlog statement can help to view execution in the log.
```sas
PUTLOG _ALL_;
PUTLOG column=;
PUTLOG "message";
```
**Ex1:**
```sas
data np_parks;
    set pg2.np_final (obs=5);
    putlog "NOTE: START DATA STEP ITERATION"; 
    keep Region ParkName AvgMonthlyVisitors Acres Size;
    length Size $ 6;
    where Type="PARK";
    format AvgMonthlyVisitors Acres comma10.;
    Type=propcase(Type);
    putlog Type=;
    AvgMonthlyVisitors=sum(DayVisits,Campers,OtherLodging)/12;
    if Acres<1000 then Size="Small";
    else if Acres<100000 then Size="Medium";
    else Size="Large";
    putlog _all_;
run;
```
5. implicit output VS explicit output statement. They can control **when** and **where** to output.

   * When an explicit OUTPUT statement is in a DATA step, the implicit OUTPUT is disabled.
   * You can create multiple tables simultaneously simply by listing more than one table on the DATA statement. Then you can use the OUTPUT statement followed by the name of the table to which SAS should write the contents of the PDV.
   * You can use the DROP= or KEEP= data set options to specify a unique list of columns for each table listed in the DATA statement. The PDV keeps track of the columns to drop from the specific output table.
```sas
table(DROP=col1 col2 ...)
table(KEEP=col1 col2 ...)
```
