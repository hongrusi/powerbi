## Context Transition

> ℹ️ `CALCULATE()` is able to turn row context into filer context

Data source

| Product | Sales |
| --- | --- |
| A | 10 |
| A | 10 |
| B | 50 |
| B | 50 |

### Task

**1. Create a calculated column using `SUM()`**

In calculated column, there is always an implicit row context. However, `SUM()` is an aggregation function that ignores this row context. `SUM()` will not calculate the sales for just that specific row.

```jsx
//Calcualted Column
TotalSales = sum(Sheet1[Sales])
```

| Product | Sales | TotalSales |
| --- | --- | --- |
| A | 10 | 120 |
| A | 10 | 120 |
| B | 50 | 120 |
| B | 50 | 120 |

**2. Turn row context to filter context using `calculate()`**

`CALCULATE()` forces a context transition from row context to filer context. It takes the current row context (which is Product) and turns it into a filter context.

```jsx
//Calcualted Column
TotalSales = CALCULATE(sum(Sheet1[Sales]))
```

| Product | Sales | TotalSales |
| --- | --- | --- |
| A | 10 | 20 |
| A | 10 | 20 |
| B | 50 | 100 |
| B | 50 | 100 |

**3.Calculate Average Sales Per product**

Method 1:

```jsx
Average Sales per product =
CALCULATE(AVERAGE(Sheet3[Sales]), 
          ALLEXCEPT(Sheet3,Sheet3[Product]))
```

- `AVERAGE(Sheet3[Sales])`: This calculates the average of the Sales column.
- `ALLEXCEPT(Sheet3,Sheet3[Product])`: This removes all filters from the Sheet3 table except for the Product column.
- `CALCULATE(..., ...)`: This modifies the filter context for the AVERAGE calculation

Method 2:

```jsx
Average Sales per product iteration v1 = 
AVERAGEX(VALUES(Sheet3[Product])
		,CALCULATE(AVERAGE(Sheet3[Sales])))
```

- `VALUES(Sheet3[Product])`: This returns a table of unique Product values and respects any filters applied to the product column
- `AVERAGEX(..., ...)`: This iterates over each unique Product value and calculates the average of the expression for each.
- `CALCULATE(AVERAGE(Sheet3[Sales]))`: For each Product, this calculates the average sales.

Method 3: 

```jsx
// Modify method 2 by using ALL() instead of VALUES() 
Average Sales per product iteration v2 = 
AVERAGEX(ALL(Sheet3[Product])
		,CALCULATE(AVERAGE(Sheet3[Sales])))
```

`ALL(Sheet3[Product])`: This returns a table of all unique Products values and ignores any filters applied to the product column. What it does is to calculate averags sales among all products.

If the visual is a table, we have following result:

| Product | Average Sales per product | Average Sales per product iteration v1 | Average Sales per product iteration v2 |
| --- | --- | --- | --- |
| A | 10 | 10 | 30 |
| B | 50 | 50 | 30 |

If the visual is a card, then the filter context (Product) is missing, so all calculated measure give same result, which essential means the averages sales value. It is not averages sales per product.

+++++++

++ 30 ++

++++++