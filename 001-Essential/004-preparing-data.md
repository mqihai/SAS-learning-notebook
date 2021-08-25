## DATA Step
```sas    
DATA=output-table;
  SET input-table;
RUN;
```
1. data step processing: this step reads and creates one row each time and then loops back to make the second row.
2. filter data with ***where,keep,drop,format*** statement

**Ex:**
```sas
libname pg1 "C:\EPG1V2-2\data";
libname output "C:\EPG1V2-2\output";
data output.storm_cat5;
   set  pg1.storm_summary;
   where MaxWindMPH>=156 and startdate>="01JAN2000"d;
	 keep Season Basin Name Type MaxWindMPH;
run; 
```

   
