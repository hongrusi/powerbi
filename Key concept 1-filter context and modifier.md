## Filter context and modifier

### Filter context

1. Driven by categories in visuals, and/or
2. by cross filter interaction from other visuals

### Modify filter context using calculate

1. `calculate` allows you to modify the filter context for a calculation
2. `filter` when used inside `CALCUALTE`, it adds filters to the current context. This function returns a table with only the rows that meets the specified conditions.
3. modifier such as `all()`, `removefilters()`, `keepfilters()`, `allexcept()`: changes filters context from the specificized table or column

Data source

| Date | Product | Sales |
| --- | --- | --- |
| 31/07/2024 | A | 10 |
| 1/08/2024 | A | 20 |
| 31/07/2024 | B | 30 |
| 1/08/2024 | B | 40 |

### Tasks

**1. Calculate Total sales, Total sales for Product A, Total Sales for Product B**

> **Both visual filter and specified condition within FILTER() function have effects on the result**
> 

```jsx
Total Sales Product A = 
CALCULATE(sum(Sheet1[Sales]),
			FILTER(Sheet1, Sheet1[Product] = "A"))

Total Sales Product B = 
CALCULATE(sum(Sheet1[Sales]),
			FILTER(Sheet1, Sheet1[Product] = "B"))
```

| Date | Product | Sum of Sales | Total Sales Product A | Total Sales Product B |
| --- | --- | --- | --- | --- |
| 31/07/2024 | A | 10 | 10 |  |
| 31/07/2024 | B | 30 |  | 30 |


**2. Show Total Sales of Product A and B, ignoring visual date and product filter context**

`ALL()` Returns all the rows in a table, or all the values in a column, ignoring any filters that might have been applied. Useful for clearing filters and creating calculations on all the rows in a table. Use `ALL()` within `FILTER()` means only apply filters specified within the filter(), all other visual filters will be ignored.

> **Only specified condition within FILTER() function have effects on the result**
> 

```jsx
Total Sales Product A v2 = 
CALCULATE(sum(Sheet1[Sales]),
			FILTER(ALL(Sheet1), Sheet1[Product] = "A"))

Total Sales Product B v2 = 
CALCULATE(sum(Sheet1[Sales]),
			FILTER(ALL(Sheet1), Sheet1[Product] = "B"))
```

| Date | Product | Sum of Sales | Total Sales Product A v2 | Total Sales Product B v2 |
| --- | --- | --- | --- | --- |
| 31/07/2024 | A | 10 | 30 | 70 |
| 31/07/2024 | B | 30 | 30 | 70 |

**3.Show Total Sales of Product A + B, ignoring visual filters context**

Using `ALL()` directly within CALCULATE means ignore all visual filters. And because no filter() function in place, so there’s no other filters are applied. It simply returns a whole table, and sums all the sales value

```jsx
Total_Sales A+B = 
CALCULATE(sum(Sheet1[Sales]),
		ALL(Sheet1))
```

| Date | Product | Total_Sales A+B |
| --- | --- | --- |
| 31/07/2024 | A | 100 |
| 1/08/2024 | A | 100 |
| 31/07/2024 | B | 100 |
| 1/08/2024 | B | 100 |
**4. Show sales % per product ignoring date filter context**

`ALLEXCEPT()` modifier is used to remove all filters from a table expression except for the ones specified explicitly.

```jsx
%Sales_product = 
DIVIDE(
		 CALCULATE(sum(Sheet1[Sales]), ALLEXCEPT(Sheet1, Sheet1[Product])), 
		 CALCULATE(sum(Sheet1[Sales]), ALL(Sheet1))
		 )
```

| Date | Product | Total Sales Product A v2 | Total Sales Product B v2 | %Sales_Product |
| --- | --- | --- | --- | --- |
| 31/07/2024 | A | 30 | 70 | 0.3 |
| 31/07/2024 | B | 30 | 70 | 0.7 |

**5.Show accumulative sales per product**



```jsx
Accumulative Sales = 
sumx(
    filter(ALL((Sheet1)),
            Sheet1[Product] = max(Sheet1[Product]) &&
            Sheet1[Date] <= max(Sheet1[Date])),
     Sheet1[Sales])
```
`ALL()` ensure all the rows in Sheet1 are returned like an unaltered copy, removing any visual filters that might be applied. 

`FILTER()` function is going to select rows that meet following conditions:

- `Sheet1[Product] = max(Sheet1[Product])`: reference to the current product in context. It does not find the maximum value. This is a common DAX pattern used to create a relationship between outer and inner evaluation context.
- `Sheet1[Date] <= max(Sheet1[Date]))`: select all dates up to and including the latest date for each product

`SUMX()` function sums the sales for each row in the filtered table

> Filter contexts are imposed by `FILTER(ALL(….))` 


| Date | Product | Sales | Accumulative Sales |
| --- | --- | --- | --- |
| 31/07/2024 | A | 10 | 10 |
| 1/08/2024 | A | 20 | 30 |
| 31/07/2024 | B | 30 | 30 |
| 1/08/2024 | B | 40 | 70 |

Alternatively, we rewrite above DAX into following format:

```jsx
Accumulative Sales v2 = 
CALCULATE(sum(Sheet1[Sales]),
			FILTER(all(Sheet1), 
				    Sheet1[Product]= max(Sheet1[Product]) &&
				    Sheet1[Date]<= max(Sheet1[Date])))
```