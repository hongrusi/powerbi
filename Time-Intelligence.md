## Time-Intelligence

Sample Data

| Date | Product | Sales |
| --- | --- | --- |
| 1/07/2023 | A | 100 |
| 1/12/2023 | A | 1000 |
| 1/1/2024 | B | 50 |
| 1/06/2024 | B | 150 |
| 1/07/2024 | A | 300 |

### TOTALYTD

(Year-to-Date)

```jsx
Total Sales YTD =
TOTALYTD(SUM(Sales[Sales]), Sales[Date],"06/30")
```

This function calculates the year-to-date total of sales up to the last date in the current filter context. It automatically handles fiscal year settings where the last day of the year is manually defined as 30 June. 

++++++

++300++

++++++

### SAMEPERIODLASTYEAR

```jsx
Sales Same Period Last Year =
CALCULATE(
    SUM(Sales[Sales]),
    SAMEPERIODLASTYEAR(Sales[Date])
) 
```

This function shifts the date context to the same period in the previous year. This means it preserves the current filter context's granularity and range, just moved back by one year.

- Single day:
If the current context is a single day (e.g., May 15, 2024), SAMEPERIODLASTYEAR will return May 15, 2023.
- Month:
If the current context is a month (e.g., May 2024), SAMEPERIODLASTYEAR will return the entire month of May 2023.
- Quarter:
If the current context is Q2 2024 (April 1 to June 30), SAMEPERIODLASTYEAR will return Q2 2023 (April 1 to June 30, 2023).
- Custom period:
If the current context is a custom date range (e.g., May 10 to June 5, 2024), SAMEPERIODLASTYEAR will return May 10 to June 5, 2023.
- Leap years:
SAMEPERIODLASTYEAR handles leap years correctly. For example, if the current date is February 29, 2024, it will return February 28, 2023, as 2023 is not a leap year.

| Date | Product | Sales | Sales Same Period Last Year |
| --- | --- | --- | --- |
| 1/07/2023 | A | 100 |  |
| 1/12/2023 | A | 1000 |  |
| 1/1/2024 | B | 50 |  |
| 1/06/2024 | B | 150 |  |
| 1/07/2024 | A | 300 | 100 |

### DATEADD

```jsx
Sales Previous Month =
CALCULATE(
    SUM(Sales[Sales]),
    DATEADD(Sales[Date], -1, MONTH)
)
```

DATEADD shifts the dates in the current filter context by a specified interval. In this case, it's shifting back by one month to calculate the previous month's sales.

| Date  | Sales | Sales Previous Month |
| --- | --- | ---  |
| 1/07/2023 | 100  |  |
| 1/12/2023 | 1000 |  |
| 1/1/2024  | 50   | 1000  |
| 1/06/2024 | 150  |  |
| 1/07/2024 | 300  | 150 |

### DATESYTD

```jsx
Running Total Sales YTD =
CALCULATE(
    SUM(Sales[Sales]),
    DATESYTD(Sales[Date])
)  
```

This function returns a table of dates from the start of the year to the last date in the current context. 

The `CALCULATE` function uses the result of `DATESYTD(Sales[Date])` to modify the filter context for the `SUM(Sales[Sales])` calculation. This means it will only sum the sales for dates from the start of the year up to the current date.

| Date  | Sales | Running Total Sales YTD |
| ---  | --- | --- |
| 1/07/2023 | 100 | 100 |
| 1/12/2023 | 1000 | 1100 |
| 1/1/2024  | 50 | 50 |
| 1/06/2024 | 150 | 200 |
| 1/07/2024 | 300 | 500 |

```jsx
// Define a custom end of the year
Running Total Sales FYTD =
CALCULATE(
    SUM(Sales[Sales]),
    DATESYTD(Sales[Date], "06/30")
)  
```

| Date | Sales | Running Total Sales FYTD |
| --- | --- | --- |
| 1/07/2023 | 100 | 100 |
| 1/12/2023 | 1000 | 1100 |
| 1/1/2024 | 50 | 1150 |
| 1/06/2024 | 150 | 1300 |
| 1/07/2024 | 300 | 300 |

### PARALLELPERIOD

```jsx
Sales Parallel Year=
CALCULATE(
    SUM(Sales[Sales]),
    PARALLELPERIOD(Sales[Date], -1, YEAR)
) 
```

PARALLELPERIOD returns a period of the same length as the dates in the current filter context, but shifted by a specified number of intervals. In this case, it's calculating sales for the previous quarter.

| Date  | Sales | Sales Parallel Year |
| --- | --- | --- |
| 1/07/2023 | 100 |  |
| 1/12/2023 | 1000 |  |
| 1/1/2024 | 50 | 1100 |
| 1/06/2024  | 150 | 1100 |
| 1/07/2024 | 300 | 1100 |