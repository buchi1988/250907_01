# 3DEXPERIENCE MQL `expand` コマンドガイド

## 1. 概要
`expand` コマンドは、指定したビジネスオブジェクトから関連するオブジェクトを関係 (Relationship) を辿って取得するためのコマンドです。BOM 展開や派生品の調査など、構造を把握する際に使用します。

## 2. 基本構文
```mql
expand bus <type> <name> <revision> [from | to | both]
       [relationship <rel1,rel2,...>]
       [type <type1,type2,...>]
       [recurse <level>]
       [select bus <expr1,expr2,...>]
       [select rel <expr1,expr2,...>]
       [where '<MQL 条件式>']
       [limit <n>] 
       [dump | dump "<sep>" | tcl | html]
```

- `<type> <name> <revision>` もしくは `id <objectId>` で開始オブジェクトを指定します。
- 方向指定をしなければ `both` (from/to 双方向) が展開されます。

## 3. 主なオプション
### 3.1 方向指定
- `from` : from 側の接続のみを展開します。
- `to`   : to 側の接続のみを展開します。
- `both` : 双方向を展開します (デフォルト)。

### 3.2 `relationship`
探索対象のリレーションシップをカンマ区切りで指定します。指定しない場合、全ての関係が対象です。

### 3.3 `type`
展開結果として取得するビジネスオブジェクトのタイプを絞り込みます。

### 3.4 `recurse`
再帰的に下位を探索する階層数を指定します。`recurse to 2` で 2 層下まで展開します。

### 3.5 `select`
- `select bus <expr>` : 取得するビジネスオブジェクトの属性や情報を指定します。
- `select rel <expr>` : リレーションシップの属性を取得します。

### 3.6 `where`
展開対象を MQL の条件式で絞り込みます。

### 3.7 `limit`
返却する子オブジェクト数を制限します。性能対策に有効です。

### 3.8 出力形式
- `dump` : パイプ区切りで出力します。
- `dump "<sep>"` : 任意の区切り文字を指定します。
- `tcl` / `html` : 出力フォーマットを変更します。

## 4. 使用例
### 4.1 基本的な展開
```mql
mql> expand bus Part A 1
```
現在リビジョンの Part "A" から、全ての関連オブジェクトを 1 階層分取得します。

### 4.2 リレーションシップとタイプを指定
```mql
mql> expand bus 12345 to rel EBOM type Part
```
ID 12345 のオブジェクトから、`EBOM` リレーションを通じて `Part` タイプのみを取得します。

### 4.3 再帰展開と属性取得
```mql
mql> expand bus 12345 from rel EBOM recurse to 2 \
          select bus name revision id \
          select rel attribute[Quantity]
```
From 側に接続された `EBOM` を 2 階層下まで展開し、子オブジェクトの `name`、`revision`、`id` と、接続リレーションの `Quantity` 属性を取得します。

### 4.4 条件でフィルタリング
```mql
mql> expand bus Part A 1 to rel EBOM \
          where "attribute[Usage] == 'Make'"
```
`Usage` 属性が `Make` の関係のみを展開します。

## 5. 注意点と Tips
- 大規模な構造を展開する場合、`limit` や `recurse` で範囲を制御し、パフォーマンスに注意します。
- `select` で取得する情報を絞ると、レスポンスが向上します。
- 出力結果は `level | rel | type | name | revision | id | ...` の順で表示されます (dump 出力時)。

## 6. 参考
- 3DEXPERIENCE Platform V6 Documentation: ENOVIA MQL Reference
- ENOVIA User Guide
