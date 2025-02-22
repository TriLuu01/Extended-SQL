# Extended-SQL
For any given MF or EMF query input the program generates an equivalent python file that computes a single scan of the db and calculates the mf or emf equivalence of the result in java

# Introduction
We are aiming to build a Query Processing Engine for Ad-Hoc OLAP queries by extending the group by statement and adding new such that clause

# Advantages
• Succinct
• Easy to maintain
• Easy to understand for non-technical person
• Potential to be more efficient
• Scalable
• Any MF/EMF query can be converted to 6 operands

# Input Files and Query Structure
• Grouping Attributes (V)
• Number of Grouping variables (n)
• Projected Attributes (S)
• Aggregate functions (F-Vector) ([F])
• Such that Conditions ([C])
• Where clause
• Having Condition (G)
# Sample Query

```cust, prod, count_1_quant, min_1_quant, max_1_quant, avg_1_quant, sum_1_quant
prod, count_1_quant, count_2_quant
2
prod
avg_quant, count_1_quant, count_2_quant
quant > avg_quant; 1.cust = 'Dan'; 2.cust != 'Dan'
None
```
This translates to
```

With q1 as
(
	Select prod, avg(quant) avg_quant
	From sales
	Group by prod
), q2 as
(
	Select sales.prod, count(sales.quant) count_1_quant
	From sales, q1
	Where cust = 'Dan' and sales.prod = q1.prod and sales.quant > q1.avg_quant
	Group by sales.prod
), q3 as
(
	Select sales.prod, count(sales.quant) count_2_quant
	From sales, q1
	Where cust != 'Dan' and sales.prod = q1.prod and sales.quant >= q1.avg_quant
	Group by sales.prod
)
Select q1.prod, q2.count_1_quant, q3.count_2_quant
From q1 natural join q2 natural join q3
Order by prod
```
# Limitations
• The operands are read from a file instead of a UI. The files is structured to take operands in a certain format. Slightest deviation from this format can cause errors and/or incorrect output
• We are using the 6 operands instead of Extended-SQL
• The project scope is only limited to the sales table because it doesn’t let the user to input which table to run the queries
• No support for minimal scan, optimization
• Limited to phi operator

