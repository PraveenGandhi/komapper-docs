---
title: "Builder"
linkTitle: "Builder"
weight: 60
description: >
  クエリのビルダー
---

## 概要 {#overview}

`QueryDsl`で構築可能なクエリ全体の一部を部品として定義し、それらを組み立てることで1つのクエリを構築できます。
部品を定義する関数をビルダーと呼びます。
ビルダー関数はすべて`org.komapper.core.dsl.query`に定義されます。

## where

Where宣言を組み立てるビルダーです。

```kotlin
val salaryWhere = where {
  e.salary greater BigDecimal(1_000)
}
val query: Query<List<Employee>> = QueryDsl.from(e).where(salaryWhere)
```

## on

On宣言を組み立てるビルダーです。

```kotlin
val departmentIdOn = on {
    e.departmentId eq d.departmentId
}
val query: Query<List<Employee>> = QueryDsl.from(e).innerJoin(d, departmentIdOn)
```

## having

Having宣言を組み立てるビルダーです。

```kotlin
val countHaving = having {
    count() greater 3
}
val query: Query<List<Int?>> = QueryDsl.from(e)
    .groupBy(e.departmentId)
    .having(countHaving)
    .select(e.departmentId)
```

## set

Assignment宣言を組み立てるビルダーです。

```kotlin
val addressAssignment = set(a) {
    a.street set "STREET 16"
}
val query: Query<Int> = QueryDsl.update(a).set(addressAssignment).where {
    a.addressId eq 1
}
```

## value

Assignment宣言を組み立てるビルダーです。

```kotlin
val addressAssignment = set(a) {
    a.street set "STREET 16"
}
val query: Query<Pair<Int, Int?>> = QueryDsl.insert(a).values(addressAssignment)
```

## innerJoin

InnerJoin要素を組み立てるビルダーです。

```kotlin
val departmentJoin = innerJoin(d) {
    e.departmentId eq d.departmentId
}
val query: Query<List<Employee>> = QueryDsl.from(e).innerJoin(departmentJoin)
```

## leftJoin

LeftJoin要素を組み立てるビルダーです。

```kotlin
val departmentJoin = leftJoin(d) {
    e.departmentId eq d.departmentId
}
val query: Query<List<Employee>> = QueryDsl.from(e).innerJoin(departmentJoin)
```


## groupBy

GroupBy要素を組み立てるビルダーです。

```kotlin
val groupByDepartmentId = groupBy(e.departmentId)
val query: Query<List<Int?>> = QueryDsl.from(e)
    .groupBy(groupByDepartmentId)
    .having {
        count() greater 3
    }
    .select(e.departmentId)
```

## orderBy

OrderBy要素を組み立てるビルダーです。

```kotlin
val orderBySalaryAndNo = orderBy( e.salary, e.employeeNo)
val query: Query<List<Employee>> = QueryDsl.from(e).orderBy(orderBySalaryAndNo)
```
