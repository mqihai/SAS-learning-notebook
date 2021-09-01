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
