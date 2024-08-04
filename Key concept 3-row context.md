## Row context

> **Calculated Columns:**
It has row context and compute for each row in the table when the column is created.
They can directly reference other columns in the same table without using any special functions.  
>**Calculated Measure:**
It does not have row context and do not have direct access to individual row values.
To work with row-level data, measures often use aggregation functions or need to create row context using functions like SUMX and MAXX.

</aside>

Data source

| Date | Product | Price | Unit | Sales |
| --- | --- | --- | --- | --- |
| 31/07/2024 | A | 10 | 1 | 10 |
| 1/08/2024 | A | 10 | 2 | 20 |
| 31/07/2024 | B | 20 | 2 | 40 |
| 1/08/2024 | B | 20 | 1 | 20 |

### Task

**1. find total sales using calculated columns**

```jsx
Sales = Sheet1(Price) * Sheet1(Unit)
```

Then use `sum()` function in calculated measure

```jsx
TotalSales = sum(Sheet1(Sales))
```

Alternatively, use calculated measure to find total sales in one step.

```jsx
SUMX(Sheet1, 
	Sheet1(Price) * Sheet1(Unit))
```

**2.Find product with highest total sales**

```jsx
MaxProductSales = 
MAXX(ALL(Sheet1[Product]), calculate(SUM(Sheet1[Sales])))
```

`ALL(Sheet1[Product])` creates a table of unique products, removing any existing filers on the Product column. This table is then used by MAXX for iteration.

`MAXX()` creates row context, which iterates over reach row (each unique product) in the table created by `ALL(Sheet1[Product])`.

`CALCULATE()` uses this row context to create a filter context for each product.

`SUM()` is an aggregation function that, by default, ignores row context unless context transition is applied (in this case context transition is applied by `CALCULATE()`)

| Product | MaxProductSales |
| --- | --- |
| A | 60 |
| B | 60 |

If visual is a card, the result would be:

+++++++

++  60  ++

+++++++

If we do **not** use `CALCULATE()`, `SUM(Sheet1[Sales])` would be evaluated in the original filter context, not taking into account the current product in the MAXX iteration, which is wrong.

```jsx
// Incorrect use of SUM() 
MaxProductSales_Wrong = 
MAXX(ALL(Sheet1[Product]), SUM(Sheet1[Sales]))
```

| Product | MaxProductSales_Wrong |
| --- | --- |
| A | 30 |
| B | 60 |

If visual is a card, the result would be:

+++++++

++  90  ++

+++++++

**3.Find the highest day sales**

```jsx
MaxDaySale = 
MAXX(ALL(Sheet1[Date]), CALCULATE(SUM(Sheet1[Sales])))
```
Similar to task 2, `ALL(Sheet1[Date])` creates a table of unique Dates.

`MAXX()` iterates over this table, creating a row context for each Date

`CALCULATE()` uses this row context to create a filter context for each Date. 

| Product | MaxDaySales |
| --- | --- |
| A | 20 |
| B | 40 |

Note: In the above visual, ‘Product’ adds additional filter context to the result. What result says in this case is for each product find the highest day sales. In order to show the highest day sales ignoring product, we can use modified DAX:

```jsx
MaxDaySale_Modified = 
MAXX(ALL(Sheet1[Date]), CALCULATE(SUM(Sheet1[Sales]), ALLEXCEPT(Sheet1, Sheet1[Date])))
```

| Product | MaxDaySales_Modified |
| --- | --- |
| A | 50 |
| B | 50 |


If we do not use `CALCUALTE()`, the `SUM(Sheet1[Sales])` would be evaluated in the original filter context which comes from visual, not taking into account the current Date in the MAXX iteration.

```jsx
// Incorrect use of SUM()
MaxDaySale_Wrong = 
MAXX(ALL(Sheet1[Date]), SUM(Sheet1[Sales]))
```

| Product | MaxDaySales_Wrong |
| --- | --- |
| A | 30 |
| B | 60 |

| Date | MaxDaySales_Wrong |
| --- | --- |
| 31/07/2024 | 50 |
| 1/08/2024 | 40 |

If visual is a card, the result would be:

+++++++

++  90  ++

+++++++