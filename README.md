# NaturalSQL by ChatDB

NaturalSQL by [ChatDB](https://chatdb.ai) is a series of models with state-of-the-art performance on Text to SQL instructions.

[HuggingFace](https://huggingface.co/chatdb/natural-sql-7b)

## Introducing NaturalSQL-7B
#### Percentage of Correct SQL Queries on Novel Schemas
*Big thanks to the folks at Defog for open sourcing the [SQL-Eval](https://github.com/defog-ai/sql-eval) framework*

![image](https://cdn-uploads.huggingface.co/production/uploads/648a374f00f7a3374ee64b99/5ynfoKPzI3_-WasQQt7qR.png)

NaturalSQL-7B is the leading model for it's size, benchmarked on SQL-Eval.

#### Example

**Question:** `Determine the customer with the largest total order value last month, and list all the products and their quantities they ordered.`

**Output**:


```sql
WITH
  LastMonthOrders AS (
    SELECT
      o.customerid,
      o.orderid,
      op.productid,
      op.quantity,
      p.productname,
      op.quantity * p.unitprice AS total_order_value
    FROM
      orders o
      JOIN order_details op ON o.orderid = op.orderid
      JOIN products p ON op.productid = p.productid
    WHERE
      o.orderdate >= date_trunc('month', CURRENT_DATE) - INTERVAL '1 month'
      AND o.orderdate < date_trunc('month', CURRENT_DATE)
  ),
  CustomerTotalOrderValue AS (
    SELECT
      customerid,
      SUM(total_order_value) AS total_value
    FROM
      LastMonthOrders
    GROUP BY
      customerid
    ORDER BY
      total_value DESC
    LIMIT
      1
  )
SELECT
  c.customerid,
  c.companyname,
  lm.productid,
  lm.productname,
  lm.quantity
FROM
  CustomerTotalOrderValue ctov
  JOIN LastMonthOrders lm ON ctov.customerid = lm.customerid
  JOIN customers c ON c.customerid = lm.customerid;
```

NaturalQuery performs really well with subqueries, ratios, and questions like above.

### License

This repo code is licensed under `apache-2.0`, the model license is `CC BY-SA 4.0`.

