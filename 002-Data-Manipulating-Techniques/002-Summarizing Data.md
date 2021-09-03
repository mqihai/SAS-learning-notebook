1. Creating an Accumulating Column
* Retaining Values in the PDV and use "+" or sum expression.
```sas
RETAIN column <initial-value>;
```
* You can use the SUM statement as a simple way to create an accumulating column. The name of the new accumulating column is on the left, and the column or constant to add for each row is on the right of the expression. The SUM statement does the following four things automatically:
  - Creates the accumulating column to the left of the plus sign and sets the initial value to 0.
  - Automatically retains the value of the accumulating column in the PDV.
  - Adds the value of the column or constant on the right of the plus sign to the accumulating column for each row.
  - Ignores missing values.
```sas
column+expression
```
