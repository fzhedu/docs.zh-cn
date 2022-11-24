# array_sortby

## 功能

对数组中的元素根据另外一个键值数组元素或者 lambda 函数生成的键值数组元素进行升序排列。

## 语法

```Haskell
ARRAY_SORTBY(array0, array1)
ARRAY_SORTBY(lambda function, array0 [, array1...])

```

## 参数说明

* `array`：需要排序的数组，支持的数据类型为 ARRAY，或者 `null`。
* `array1`：排序键值数组，支持的数据类型为 ARRAY，或者 `null`。
* `lambda function`：生成键值数组的 lambda 函数。

## 返回值说明

返回的数据类型为 ARRAY。

## 注意事项

* 只支持升序。
* `null` 会排在前面。
* 如果需要降序排列，可以对排序后的结果，调用 `reverse` 函数。
* 返回结果数组的中的元素类型和参数 `array0` 中的元素类型一致，`null` 属性一致。
* 如果键值数组为 `null`，排序数据保持不变

## 示例

下面的示例使用如下数据表进行介绍。

将排序数组 `c3` 按照 `c2` 的值进行升序排序 

```Plain Text
mysql> select c3, c2, array_sortby(c3,c2) from test_array order by c1;
+-------------+-------------+----------------------+
| c3          | c2          | array_sortby(c3, c2) |
+-------------+-------------+----------------------+
| [82,1,4]    | [3,4,5]     | [82,1,4]             |
| [23]        | NULL        | [23]                 |
| NULL        | [2,4]       | NULL                 |
| NULL        | NULL        | NULL                 |
| []          | []          | []                   |
| []          | NULL        | []                   |
| NULL        | []          | NULL                 |
| [3,6]       | [null,null] | [3,6]                |
| [null,null] | [432,23]    | [null,null]          |
+-------------+-------------+----------------------+
```

将排序数组 `c3` 按照 lambda 表达式生成的数组元素进行升序排序 

```Plain Text
mysql> select c3, c2, array_sortby((x,y) -> y, c3,c2) from test_array order by c1;
+-------------+-------------+--------------------------------------------------+
| c3          | c2          | array_sortby(c3, array_map((x, y) -> y, c3, c2)) |
+-------------+-------------+--------------------------------------------------+
| [82,1,4]    | [3,4,5]     | [82,1,4]                                         |
| [23]        | NULL        | [23]                                             |
| NULL        | [2,4]       | NULL                                             |
| NULL        | NULL        | NULL                                             |
| []          | []          | []                                               |
| []          | NULL        | []                                               |
| NULL        | []          | NULL                                             |
| [3,6]       | [null,null] | [3,6]                                            |
| [null,null] | [432,23]    | [null,null]                                      |
+-------------+-------------+--------------------------------------------------+
```
