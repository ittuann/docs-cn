---
title: 字符串函数
aliases: ['/docs-cn/dev/functions-and-operators/string-functions/','/docs-cn/dev/reference/sql/functions-and-operators/string-functions/','/docs-cn/dev/sql/string-functions/']
---

# 字符串函数

TiDB 支持使用大部分 MySQL 5.7 中提供的[字符串函数](https://dev.mysql.com/doc/refman/5.7/en/string-functions.html)、一部分 MySQL 8.0 中提供的[字符串函数](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html)和一部分 Oracle 21 所提供的[函数](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlqr/SQL-Functions.html#GUID-93EC62F8-415D-4A7E-B050-5D5B2C127009)。

关于 Oracle 函数和 TiDB 函数的对照关系，请参考 [Oracle 与 TiDB 函数和语法差异对照](/oracle-functions-to-tidb.md)。

## 支持的函数

### [`ASCII()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_ascii)

`ASCII()` 函数用于获取输入的参数中最左字符的 ASCII 值。该参数可以为字符串或数字。

- 如果输入参数不为空，该函数返回参数中最左字符的 ASCII 值。
- 如果输入参数为空，该函数返回 `0`。
- 如果输入参数为 `NULL`，该函数返回 `NULL`。

> **注意：**
>
> `ASCII()` 只能处理那些用 8 个二进制数字（即单个字节）来表示的字符。

示例：

```sql
SELECT ASCII('A');

+------------+
| ASCII('A') |
+------------+
|         65 |
+------------+
```

```sql
SELECT ASCII('TiDB');

+---------------+
| ASCII('TiDB') |
+---------------+
|            84 |
+---------------+
```

```sql
SELECT ASCII(23);

+-----------+
| ASCII(23) |
+-----------+
|        50 |
+-----------+
```

### [`BIN()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_bin)

`BIN()` 函数用于将输入的参数转换为其二进制值的字符串表示形式。该参数可以为字符串或数字。

- 如果输入参数为正数，该函数返回该参数的二进制值的字符串表示形式。
- 如果输入参数为负数，该函数会将该参数的绝对值转换为其二进制值，然后对二进制值的每位取反（`0` 变为 `1`，`1` 变为 `0`），最后加上 `1`。
- 如果输入参数为字符串，且该字符串中只包含数字，该函数将按照该数字返回结果。例如，`"123"` 与 `123` 的返回结果相同。
- 如果输入参数为字符串，且该字符串第一个字符不是数字（如 `"q123"`），该函数返回 `0`。
- 如果输入参数为字符串，且该字符串由数字和非数字组成，该函数将按照该参数中最前面连续的数字返回结果。例如，`“123q123”` 与 `123` 的返回结果相同。
- 如果输入参数为 `NULL`，该函数返回 `NULL`。

示例：

```sql
SELECT BIN(123);

+----------+
| BIN(123) |
+----------+
| 1111011  |
+----------+
```

```sql
SELECT BIN(-7);

+------------------------------------------------------------------+
| BIN(-7)                                                          |
+------------------------------------------------------------------+
| 1111111111111111111111111111111111111111111111111111111111111001 |
+------------------------------------------------------------------+
```

```sql
SELECT BIN("123q123");

+----------------+
| BIN("123q123") |
+----------------+
| 1111011        |
+----------------+
```

### [`BIT_LENGTH()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_bit-length)

返回字符串的位长度

### [`CHAR()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_char)

返回由整数的代码值所给出的字符组成的字符串

### [`CHAR_LENGTH()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_char-length)

返回字符串的字符长度

### [`CHARACTER_LENGTH()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_character-length)

与 `CHAR_LENGTH()` 功能相同

### [`CONCAT()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_concat)

返回连接的字符串

### [`CONCAT_WS()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_concat-ws)

返回由分隔符连接的字符串

### [`ELT()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_elt)

返回指定位置的字符串

### [`EXPORT_SET()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_export-set)

返回一个字符串，其中值位中设置的每个位，可以得到一个 on 字符串，而每个未设置的位，可以得到一个 off 字符串

### [`FIELD()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_field)

返回参数在后续参数中出现的第一个位置

### [`FIND_IN_SET()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_find-in-set)

返回第一个参数在第二个参数中出现的位置

### [`FORMAT()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_format)

`FORMAT(X,D[,locale])` 函数用于将数字 `X` 格式化为类似于 `“#,###,###.##”` 的格式，四舍五入保留 `D` 位小数，并将结果作为字符串返回。

参数：

- `X`：要格式化的数字。可以是直接的数字值、数字字符串、或科学记数法格式的数字。
- `D`：指定返回值的小数位数。该函数根据 `D` 对 `X` 进行四舍五入。如果 `D` 大于 `X` 的实际小数位数，则会在结果中填充相应长度的零。
- `[locale]`：指定一个区域设置，用于结果中数字的小数点、千位分隔符和分隔符之间的分组。合法的区域设置值与 [`lc_time_names`](https://dev.mysql.com/doc/refman/8.3/en/server-system-variables.html#sysvar_lc_time_names) 系统变量的合法值相同。如果未指定或者设置为 `NULL`，则默认使用 `'en_US'` 区域设置。该参数可选。

行为细节：

- 如果输入的第一个参数为字符串，且该字符串中只包含数字时，该函数将按照该数字返回结果。例如，`FORMAT('12.36', 1)` 与 `FORMAT(12.36, 1)` 的返回结果相同。
- 如果输入的第一个参数为科学计数法（`E/e`）表示的数字时，该函数将按照该数字返回结果。例如，`FORMAT('1E2', 3)`），函数返回 `100.000`。
- 如果输入的第一个参数为非数字开头的字符串时，该函数除了返回零值外，还返回一个警告 `(Code 1292)`。例如，`FORMAT('q12.36', 5)` 函数返回 `0.00000`，还会包含一个警告 `Warning (Code 1292): Truncated incorrect DOUBLE value: 'q12.36'`。
- 如果输入的第一个参数为数字和非数字混合的字符串时，该函数将基于该参数中开头连续的数字部分返回结果，还返回一个警告 `(Code 1292)`。例如，`FORMAT('12.36q56.78', 1)` 与 `FORMAT('12.36', 1)` 的返回的数字结果相同，但 `FORMAT('12.36q56.78', 1)` 还会包含一个警告 `Warning (Code 1292): Truncated incorrect DOUBLE value: '12.36q56.78'`。
- 如果输入的第二个参数为零或负数，该函数将四舍五入小数部分并返回整数。
- 如果输入的任意参数为 `NULL`，函数将返回 `NULL`。

示例：

格式化数字 12.36 到不同的小数位数：

```sql
mysql> SELECT FORMAT(12.36, 1);
+------------------+
| FORMAT(12.36, 1) |
+------------------+
| 12.4             |
+------------------+
```

```sql
mysql> SELECT FORMAT(12.36, 5);
+------------------+
| FORMAT(12.36, 5) |
+------------------+
| 12.36000         |
+------------------+
```

```sql
mysql> SELECT FORMAT(1234.56, 1, 'en_US');
+-----------------------------+
| FORMAT(1234.56, 1, 'en_US') |
+-----------------------------+
| 1,234.6                     |
+-----------------------------+
```

### [`FROM_BASE64()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_from-base64)

`FROM_BASE64(str)` 函数用于对 [Base64](https://datatracker.ietf.org/doc/html/rfc4648) 编码的字符串进行解码，并将解码结果以十六进制字符串的形式返回。

- 此函数接受一个单一参数，即需要解码的 Base64 编码字符串。
- 如果输入参数为 `NULL` 或无效的 Base64 编码字符串，`FROM_BASE64()` 函数将返回 `NULL`。

示例：

以下示例解码 Base64 编码的字符串 `'SGVsbG8gVGlEQg=='`，该字符串是 `'Hello TiDB'` 经过 [`TO_BASE64()`](#to_base64) 函数编码的结果。

```sql
mysql> SELECT TO_BASE64('Hello TiDB');
+-------------------------+
| TO_BASE64('Hello TiDB') |
+-------------------------+
| SGVsbG8gVGlEQg==        |
+-------------------------+

mysql> SELECT FROM_BASE64('SGVsbG8gVGlEQg==');
+------------------------------------------------------------------+
| FROM_BASE64('SGVsbG8gVGlEQg==')                                  |
+------------------------------------------------------------------+
| 0x48656C6C6F2054694442                                           |
+------------------------------------------------------------------+
```

```sql
mysql> SELECT CONVERT(FROM_BASE64('SGVsbG8gVGlEQg==') USING utf8mb4);
+--------------------------------------------------------+
| CONVERT(FROM_BASE64('SGVsbG8gVGlEQg==') USING utf8mb4) |
+--------------------------------------------------------+
| Hello TiDB                                             |
+--------------------------------------------------------+
```

以下示例解码 Base64 编码的数字 `MTIzNDU2`，该字符串是 `123456` 经过 [`TO_BASE64()`](#to_base64) 函数编码的结果。

```sql
mysql> SELECT FROM_BASE64('MTIzNDU2');
+--------------------------------------------------+
| FROM_BASE64('MTIzNDU2')                          |
+--------------------------------------------------+
| 0x313233343536                                   |
+--------------------------------------------------+
```

### [`HEX()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_hex)

返回一个十进制数或字符串值的 16 进制表示

### [`INSERT()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_insert)

在指定位置插入一个子字符串，最多不超过指定字符数

### [`INSTR()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_instr)

返回第一次出现的子字符串的索引

### [`LCASE()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_lcase)

与 `LOWER()` 功能相同

### [`LEFT()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_left)

返回最左侧指定长度的字符

### [`LENGTH()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_length)

返回字符串长度，单位为字节

### [`LIKE`](https://dev.mysql.com/doc/refman/8.0/en/string-comparison-functions.html#operator_like)

进行简单模式匹配

### [`LOCATE()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_locate)

返回第一次出现的子字符串的位置

### [`LOWER()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_lower)

返回全小写的参数

### [`LPAD()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_lpad)

返回字符串参数，左侧添加指定字符串

### [`LTRIM()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_ltrim)

去掉前缀空格

### [`MAKE_SET()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_make-set)

返回一组用逗号分隔的字符串，这些字符串的位数与给定的 bits 参数对应

### [`MID()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_mid)

返回一个以指定位置开始的子字符串

### [`NOT LIKE`](https://dev.mysql.com/doc/refman/8.0/en/string-comparison-functions.html#operator_not-like)

否定简单模式匹配

### [`NOT REGEXP`](https://dev.mysql.com/doc/refman/8.0/en/regexp.html#operator_not-regexp)

`REGEXP` 的否定形式

### [`OCT()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_oct)

返回一个数值的八进制表示，形式为字符串

### [`OCTET_LENGTH()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_octet-length)

与 `LENGTH()` 功能相同

### [`ORD()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_ord)

返回该参数最左侧字符的字符编码

### [`POSITION()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_position)

与 `LOCATE()` 功能相同

### [`QUOTE()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_quote)

使参数逃逸，为了在 SQL 语句中使用

### [`REGEXP`](https://dev.mysql.com/doc/refman/8.0/en/regexp.html#operator_regexp)

使用正则表达式匹配模式

### [`REGEXP_INSTR()`](https://dev.mysql.com/doc/refman/8.0/en/regexp.html#function_regexp-instr)

返回满足正则的子字符串的第一个索引位置（与 MySQL 不完全兼容，具体请参考[正则函数与 MySQL 的兼容性](#正则函数与-mysql-的兼容性)）

### [`REGEXP_LIKE()`](https://dev.mysql.com/doc/refman/8.0/en/regexp.html#function_regexp-like)

判断字符串是否满足正则表达式（与 MySQL 不完全兼容，具体请参考[正则函数与 MySQL 的兼容性](#正则函数与-mysql-的兼容性)）

### [`REGEXP_REPLACE()`](https://dev.mysql.com/doc/refman/8.0/en/regexp.html#function_regexp-replace)

替换满足正则表达式的子字符串（与 MySQL 不完全兼容，具体请参考[正则函数与 MySQL 的兼容性](#正则函数与-mysql-的兼容性)）

### [`REGEXP_SUBSTR()`](https://dev.mysql.com/doc/refman/8.0/en/regexp.html#function_regexp-substr)

返回满足正则表达式的子字符串（与 MySQL 不完全兼容，具体请参考[正则函数与 MySQL 的兼容性](#正则函数与-mysql-的兼容性)）

### [`REPEAT()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_repeat)

以指定次数重复一个字符串

### [`REPLACE()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_replace)

替换所有出现的指定字符串

### [`REVERSE()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_reverse)

反转字符串里的所有字符

### [`RIGHT()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_right)

返回指定数量的最右侧的字符

### [`RLIKE`](https://dev.mysql.com/doc/refman/8.0/en/regexp.html#operator_regexp)

与 `REGEXP` 功能相同

### [`RPAD()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_rpad)

以指定次数添加字符串

### [`RTRIM()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_rtrim)

去掉后缀空格

### [`SPACE()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_space)

返回指定数量的空格，形式为字符串

### [`STRCMP()`](https://dev.mysql.com/doc/refman/8.0/en/string-comparison-functions.html#function_strcmp)

比较两个字符串

### [`SUBSTR()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_substr)

返回指定的子字符串

### [`SUBSTRING()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_substring)

返回指定的子字符串

### [`SUBSTRING_INDEX()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_substring-index)

从一个字符串中返回指定出现次数的定界符之前的子字符串

### [`TO_BASE64()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_to-base64)

返回转化为 base-64 表示的字符串参数

### [`TRANSLATE()`](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/TRANSLATE.html#GUID-80F85ACB-092C-4CC7-91F6-B3A585E3A690)

将字符串中出现的所有指定字符替换为其它字符。这个函数不会像 Oracle 一样将空字符串视为`NULL`

### [`TRIM()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_trim)

去掉前缀和后缀空格

### [`UCASE()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_ucase)

与 `UPPER()` 功能相同

### [`UNHEX()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_unhex)

返回一个数的十六进制表示，形式为字符串

### [`UPPER()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_upper)

参数转换为大写形式

### [`WEIGHT_STRING()`](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_weight-string)

返回字符串的权重

## 不支持的函数

* `LOAD_FILE()`
* `MATCH()`
* `SOUNDEX()`

## 正则函数与 MySQL 的兼容性

本节介绍 TiDB 中正则函数 `REGEXP_INSTR()`、`REGEXP_LIKE()`、`REGEXP_REPLACE()`、`REGEXP_SUBSTR()` 与 MySQL 的兼容情况。

### 语法兼容性

MySQL 的实现使用的是 [ICU](https://github.com/unicode-org/icu) (International Components for Unicode) 库，TiDB 的实现使用的是 [RE2](https://github.com/google/re2) 库，两个库之间的语法差异可以查阅 [ICU 文档](https://unicode-org.github.io/icu/userguide/)和 [RE2 文档](https://github.com/google/re2/wiki/Syntax)。

### 匹配模式 `match_type` 兼容性

TiDB 与 MySQL 在 `match_type` 上的差异：

- TiDB 中 `match_type` 可选值为：`"c"`、`"i"`、`"m"`、`"s"`。MySQL 中 `match_type` 可选值为：`"c"`、`"i"`、`"m"`、`"n"`、`"u"`。
- TiDB 中 `"s"` 对应 MySQL 中的 `"n"`，即 `.` 字符匹配行结束符。

    例如：MySQL 中 `SELECT REGEXP_LIKE(a, b, "n") FROM t1;` 在 TiDB 中需要修改为 `SELECT REGEXP_LIKE(a, b, "s") FROM t1;`。

- TiDB 不支持 `match_type` 为 `"u"`。

### 数据类型兼容性

TiDB 与 MySQL 在二进制字符串 (binary string) 数据类型上的差异：

- MySQL 8.0.22 及以上版本中正则函数不支持二进制字符串，具体信息可查看 [MySQL 文档](https://dev.mysql.com/doc/refman/8.0/en/regexp.html)。但在实际使用过程中，如果所有参数或者返回值的数据类型都是二进制字符串，则正则函数可以正常使用，否则报错。
- TiDB 目前完全禁止使用二进制字符串，无论什么情况都会报错。

### 其它兼容性

TiDB 与 MySQL 在替换空字符串上的差异：

下面以 `REGEXP_REPLACE("", "^$", "123")` 为例：

- MySQL 不会对空串进行替换，其结果为 `""`。
- TiDB 会对空串进行替换，其结果为 `"123"`。
