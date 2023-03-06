# transform_keys

## 功能

对 MAP 中所有的 key-value 对的 key 进行 lambda 转换。有关 Lambda 表达式的详细信息，参见 [Lambda expression](../Lambda_expression.md)。该函数从 2.5 版本开始支持。

StarRocks 从 2.5 版本开始支持查询数据湖中的复杂数据类型 MAP 和 STRUCT。您可以通过 StarRocks 提供的 external catalog 方式来查询 Apache Hive™，Apache Hudi，Apache Iceberg 中的 MAP 和 STRUCT 数据。仅支持查询 ORC 和 Parquet 类型文件。

想了解如何使用 external catalog 查询外部数据源，参见 [Catalog 概述](../../../data_source/catalog/catalog_overview.md) 和对应的 catalog 文档。

## 语法

```Haskell
map transform_keys(lambda func, any_map)
```
lambda func 也可以放在 any_map 之后， 即:

```Haskell
map transform_keys(any_map, lambda func)
```

## 参数说明

`any_map`:  要进行运算 的 MAP 值。

`lambda func`: 对 `any_map` 的所有 key 进行转换的 lambda 函数。

## 返回值说明

返回 map, 它的 key 的类型由 lambda 函数的运算结果决定；它的 value 的类型跟输入的 any_map 中的 value 的类型一样。

如果输入参数是 NULL，结果也是 NULL。如果 MAP 中的某个 key 或 value 是 NULL，该 NULL 值正常返回。

## 示例

```SQL
mysql> select transform_keys((k,v)->(k+1>length(v)), col_map) from (select map_from_arrays([1,3,null,2,null],['ab','cdd',null,null,'abc']) as col_map)A;
+----------------------------------------------------------+
| transform_keys((k, v) -> (k + 1) > (length(v)), col_map) |
+----------------------------------------------------------+
| {0:"ab",1:"cdd",null:null,null:null,null:"abc"}          |
+----------------------------------------------------------+
1 row in set (0.01 sec)
```
