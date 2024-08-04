## Variables

> Variables are evaluated once and forever when it is defined, that is taking a snapshot of the column/measure’s values at that moment, outside of any other context. In another sense, it is a constant value


Data source

| Date | Product | Sales |
| --- | --- | --- |
| 31/07/2024 | A | 10 |
| 1/08/2024 | A | 20 |
| 31/07/2024 | B | 30 |
| 1/08/2024 | B | 40 |


### Task

**1. Calculate total sales difference between Product A and B, ignoring date filter from visual**

```jsx
Sales difference A - B = 
var _B = 
CALCULATE(sum(Sheet1[Sales]),
FILTER(ALL(Sheet1), Sheet1[Product] = "B"))

var _A =
CALCULATE(sum(Sheet1[Sales]),
FILTER(ALL(Sheet1), Sheet1[Product] = "A"))

Var _dif =
CALCULATE(_A - _B)
return
_dif
```

| Date | Product | Sales difference A - B |
| --- | --- | --- |
| 31/07/2024 | A | -40 |
| 31/07/2024 | B | -40 |

> **Variable is stored as a constant, reference to it means reference to a constant.**
> 



```jsx
//Incorrect use of variable
Sales difference A - B v2 = 
var _sales = sum(Sheet1[Sales])

var _B = 
CALCULATE(_sales,
			FILTER(ALL(Sheet1), Sheet1[Product] = "B"))

var _A =
CALCULATE(_sales,
			FILTER(ALL(Sheet1), Sheet1[Product] = "A"))

Var _dif =
CALCULATE(_A - _B)
return
_dif
```

`var _sales = sum(Sheet1[Sales])` stores total sales, ignoring all context, as a constant value (in this case is 100) and it will not change later on.

`var _B` refers to `var _sales` meaning it refers to a constant value 100.

| Date | Product | Sales difference A - B v2 |
| --- | --- | --- |
| 31/07/2024 | A | 0 |
| 31/07/2024 | B | 0 |


**2. Calculate Sales difference between each product with highest sales product**

```jsx
Sales Comparison = 
var _currentproductsale =
CALCULATE(SUM(Sheet1[Sales]),
FILTER(ALL(Sheet1), 
    Sheet1[Product] = MAX(Sheet1[Product])))

var _highestproductsale =
maxx(SUMMARIZE(ALL(Sheet1), Sheet1[Product], "HighestSales",sum(Sheet1[Sales])),
     [HighestSales])

return
_currentproductsale - _highestproductsale
```

| Product | Sum of Sales | Sales Comparison |
| --- | --- | --- |
| A | 30 | -40 |
| B | 70 | 0 |

Alternative ways to find the highest product sales

```jsx
Highestproductsales = 
MAXX(ALL(Sheet1[Product]), CALCULATE(SUM(Sheet1[Sales])))
```

`MAXX()` iterates over a table and returns the maximum value of an expression evaluated for each row. This function creates a row context.

`ALL(Sheet1[Product])` creates a table of all unique products, removing any filters on the Product column

`CALCULATE(SUM(Sheet1[Sales]))` this expression evaluate for each product the total sales. calculate() per se recognize current filter context and turns row context into filter context.