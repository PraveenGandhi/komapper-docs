---
title: "Logging"
linkTitle: "Logging"
weight: 50
description: >
  ロギング
---

## 概要 {#overview}

KomapperはSQLやトランザクションに関するログを出力します。

出力先のデフォルトは標準出力ですが変更可能です。

## ログカテゴリ {#log-category}

Komapperが出力するログのカテゴリは以下に示す4つです。

- org.komapper.SQL
- org.komapper.SQL_WITH_ARGS
- org.komapper.TRANSACTION
- org.komapper.OTHER

### org.komapper.SQL

このカテゴリのログは次のようなバインド変数である`?`が含まれた形式のSQLでdebugレベルで出力されます。

```sql
insert into ADDRESS (STREET, VERSION, CREATED_AT, UPDATED_AT) values (?, ?, ?, ?)
select t0_.ADDRESS_ID, t0_.STREET, t0_.VERSION, t0_.CREATED_AT, t0_.UPDATED_AT from ADDRESS as t0_ where t0_.ADDRESS_ID = ?
```

### org.komapper.SQL_WITH_ARGS

このカテゴリのログは次のようなバインド変数である`?`が実際の引数で置換された形式のSQLでtraceレベルで出力されます。

```sql
insert into ADDRESS (STREET, VERSION, CREATED_AT, UPDATED_AT) values ('street A', 0, '2021-07-31T21:23:24.511', '2021-07-31T21:23:24.511')
select t0_.ADDRESS_ID, t0_.STREET, t0_.VERSION, t0_.CREATED_AT, t0_.UPDATED_AT from ADDRESS as t0_ where t0_.ADDRESS_ID = 1
```

### org.komapper.TRANSACTION

このカテゴリのログは次のようなトランザクションの開始やコミットを示すメッセージでtraceレベルで出力されます。

```sql
The transaction "83d5c2e5-8d3b-4a45-a5bb-215f846a0327" has begun.
The transaction "83d5c2e5-8d3b-4a45-a5bb-215f846a0327" has committed.
```

### org.komapper.OTHER

このカテゴリは他のどのカテゴリにも属さないログを表します。
デフォルトでは利用されていません。

## SLF4Jの利用 {#slf4j}

[SLF4J](http://www.slf4j.org/) を利用してログ出力するにはkomapper-slf4jモジュールをGradleの依存関係の宣言に含めます。

```kotlin
val komapperVersion: String by project

dependencies {
    runtimeOnly("org.komapper:komapper-slf4j:$komapperVersion")
}
```

また、SLF4Jの実装として [Logback](http://logback.qos.ch/) を使う場合はlogback-classicモジュールも依存関係の宣言に含めます。

```kotlin
val komapperVersion: String by project

dependencies {
    runtimeOnly("org.komapper:komapper-slf4j:$komapperVersion")
    runtimeOnly("ch.qos.logback:logback-classic:1.2.5")
}
```

{{< alert title="Note" >}}
Komapperが提供する各種starterモジュールはSLF4JとLogbackの設定を含んでいます。
starterモジュールを使う場合、上記の設定は不要です。
{{< /alert >}}

### LogbackでSQLのログ出力をする場合の設定例 {#logback-example}

次のようなlogback.xmlをsrc/main/resourcesの下に配置すると、バインド変数`?`が含まれた形式のSQLがコンソールに出力されます。

```xml
<configuration>
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>
    
    <logger name="org.komapper.SQL" level="debug"/>

    <root level="info">
        <appender-ref ref="STDOUT" />
    </root>
</configuration>
```