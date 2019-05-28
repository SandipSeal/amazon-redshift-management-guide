# BIT\_OR Function<a name="r_BIT_OR"></a>

## Syntax<a name="r_BIT_OR-synopsis"></a>

```
BIT_OR ( [DISTINCT | ALL] expression )
```

## Arguments<a name="r_BIT_OR-arguments"></a>

 *expression *   
The target column or expression that the function operates on\. This expression must have an INT, INT2, or INT8 data type\. The function returns an equivalent INT, INT2, or INT8 data type\.

DISTINCT \| ALL  
With the argument DISTINCT, the function eliminates all duplicate values for the specified expression before calculating the result\. With the argument ALL, the function retains all duplicate values\. ALL is the default\. See [DISTINCT Support for Bit\-Wise Aggregations](c_bitwise_aggregate_functions.md#distinct-support-for-bit-wise-aggregations)\.