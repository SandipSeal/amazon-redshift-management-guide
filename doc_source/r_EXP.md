# EXP Function<a name="r_EXP"></a>

 The EXP function returns the exponential value in scientific notation for a numeric expression\. 

## Syntax<a name="r_EXP-synopsis"></a>

```
EXP (expression)
```

## Argument<a name="r_EXP-argument"></a>

 *expression*   
The expression must be an INTEGER, DECIMAL, or DOUBLE PRECISION data type\. 

## Return Type<a name="r_EXP-return-type"></a>

EXP returns a DOUBLE PRECISION number\. 

## Example<a name="r_EXP-example"></a>

Use the EXP function to forecast ticket sales based on a continuous growth pattern\. In this example, the subquery returns the number of tickets sold in 2008\. That result is multiplied by the result of the EXP function, which specifies a continuous growth rate of 7% over 10 years\. 

```
select (select sum(qtysold) from sales, date
where sales.dateid=date.dateid
and year=2008) * exp((7::float/100)*10) qty2010;

qty2010
------------------
695447.483772222
(1 row)
```