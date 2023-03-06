# map_filter

## 功能

根据设定的过滤函数返回 MAP 中匹配的 key-value 对。该过滤函数可以是普通的 boolean 数组，也可以是灵活的 Lambda 函数。有关 Lambda 表达式的详细信息，参见 [Lambda expression](../Lambda_expression.md)。该函数从 2.5 版本开始支持。

StarRocks 从 2.5 版本开始支持查询数据湖中的复杂数据类型 MAP 和 STRUCT。您可以通过 StarRocks 提供的 external catalog 方式来查询 Apache Hive™，Apache Hudi，Apache Iceberg 中的 MAP 和 STRUCT 数据。仅支持查询 ORC 和 Parquet 类型文件。

想了解如何使用 external catalog 查询外部数据源，参见 [Catalog 概述](../../../data_source/catalog/catalog_overview.md) 和对应的 catalog 文档。

## 语法

```Haskell
map map_filter(any_map, array<boolean>)
map map_filter(lambda func, any_map)
```

- `map_filter(any_map, array<boolean>)`

  保留 `any_map` 在 `array<boolean>` 中一一对应位置的值为 `true` 的 key-value 对。

- `map_filter(lambda func, any_map)`

  保留 `any_map` 中应用 `lambda func` 后值为 `true` 的 key-value 对。

## 参数说明

`any_map`:  要进行过滤运算 的 MAP 值。

`array<boolean>`: 进行过滤的 boolean 数组

`lambda func`: 过滤 `any_map` 的 lambda 函数

## 返回值说明

返回 map, 它的类型跟输入的 any_map 一样。

如果输入参数 `any_map` 是 NULL，结果也是 NULL; 如果 `array<boolean>` 是 null，结果是空 MAP。如果 MAP 中的某个 key 或 value 是 NULL，该 NULL 值正常返回。

## 示例

### 不使用 lambda 表达式

```SQL
mysql> select map_filter(col_map, array<boolean>[0,0,0,1,1]) from (select map_from_arrays([1,3,null,2,null],['ab','cdd',null,null,'abc']) as col_map)A;
+----------------------------------------------------+
| map_filter(col_map, ARRAY<BOOLEAN>[0, 0, 0, 1, 1]) |
+----------------------------------------------------+
| {2:null,null:"abc"}                                |
+----------------------------------------------------+
1 row in set (0.02 sec)

mysql> select map_filter(null, array<boolean>[0,0,0,1,1]);
+-------------------------------------------------+
| map_filter(NULL, ARRAY<BOOLEAN>[0, 0, 0, 1, 1]) |
+-------------------------------------------------+
| NULL                                            |
+-------------------------------------------------+
1 row in set (0.02 sec)

mysql> select map_filter(col_map, null) from (select map_from_arrays([1,3,null,2,null],['ab','cdd',null,null,'abc']) as col_map)A;
+---------------------------+
| map_filter(col_map, NULL) |
+---------------------------+
| {}                        |
+---------------------------+
1 row in set (0.01 sec)
```

### 使用 lambda 表达式
```SQL

mysql> select map_filter((k,v) -> v is not null,col_map) from (select map_from_arrays([1,3,null,2,null],['ab','cdd',null,null,'abc']) as col_map)A;
+------------------------------------------------+
| map_filter((k,v) -> v is not null, col_map)    |
+------------------------------------------------+
| {1:"ab",3:"cdd",2:null}                        |
+------------------------------------------------+
1 row in set (0.02 sec)
```
