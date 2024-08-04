## Top N

Sample Data

| Date | Product | Sales |
| --- | --- | --- |
| 1/07/2023 | A | 100 |
| 1/12/2023 | A | 1000 |
| 1/1/2024 | B | 50 |
| 1/06/2024 | B | 150 |
| 1/07/2024 | A | 300 |

### Task

**1. Find product name with highest sales.**

```jsx
// create a totalsales measure
TotalSales = SUM(Sales[Sales])

// find the product name with highest sales 
Top Product by Sales = 
    TOPN(1, VALUES(Sales[Product]), [TotalSales])
```

- Measure Evaluation:
When you define [TotalSales] as a separate measure, it is automatically wrapped in an implicit CALCULATE(). When you reference this meaasure, it is evaluated in the current filter context each time it's referenced.
- TOPN() Behavior:
TOPN() iterates over each value in the specified table (in this case, each unique product from VALUES(Sales[Product])), creating a row context for each iteration.
- Filter Context Preservation:
When [TotalSales] is evaluated inside TOPN(), it inherits the filter context from where the [*Top Product by Sales*] measure is being used (e.g., in a card visual).
- Row Context Transition:
For each product iteration in TOPN(), the row context (the current product) is transformed into a filter context. This is then combined with the existing filter context.
- Measure Recalculation:
The [TotalSales] measure is recalculated for each product, considering both the overall filter context and the product-specific filter context.

Above two measures can be combined into one

```jsx
Top Product by Sales = 
TOPN(1, 
    VALUES(Sales[Product]), 
    calculate(sum(Sales[Sales])))
```

This measure returns a table with one row. 

**2. Show products’ names with highest and second highest sales in a card visual**

Card visual expects a single scalar value. To show the top 2 product names in a card visual, you need to concatenate the results into a single string. 

```jsx
Top 2 Products by Sales = 
CONCATENATEX(
    TOPN(2, 
         VALUES(Sales[Product]), 
         CALCULATE(SUM(Sales[Sales]))),
    Sales[Product],
    ", "
)
```

- The inner `TOPN()` function returns a table with only one column, Sales[Product], and selects the top 2 products based on their sales.
- `CONCATENATEX()` is used to iterate over the results of `TOPN()` and combine the product names into a single string.
- The second argument of `CONCATENATEX (Sales[Product])` specifies which column to use for the concatenation. `CONCATENATEX()` is designed to work with tables that might have multiple columns. For consistency, it always requires you to specify which column or expression to use, even when the input table has only one column
- The third argument `(", ")` is the delimiter used between the product names.

If you want to include the sales amounts as well, you could modify the measure like this:

```jsx
Top 2 Products with Sales = 
CONCATENATEX(
    TOPN(2, 
         VALUES(Sales[Product]), 
         CALCULATE(SUM(Sales[Sales]))),
    Sales[Product] & 
    ": " & 
    FORMAT(CALCULATE(SUM(Sales[Sales])), "$#,##0"),
    UNICHAR(10)
)
```

The UNICHAR(10) used as the delimiter adds a line break between each product.

+++++++++++

A: $400

B: &1,200

+++++++++++

### **Potential Challenges and Considerations**

While the TOPN() function is a powerful tool, there are a few potential challenges that users should know:

1. **Ties and Duplicates**: When there are ties or duplicates in the ranking criteria, the TOPN() function may return more than N values. For instance, if two products have the same sales amount and both fall within the top 3, the TOPN() function will return both products.
2. **Performance Impact**: Using the TOPN() function on large datasets or complex measures can affect performance. Optimize DAX expressions and apply filtering techniques to reach optimal performance.