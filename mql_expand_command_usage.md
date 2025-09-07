# 3DEXPERIENCE の MQL「expand」コマンド使用ガイド

## 1. expand コマンドとは
- MQL（Matrix Query Language）でオブジェクト間の接続や構造をたどるためのコマンド。
- 例えば、あるビジネスオブジェクトの接続関係を確認するには次のように使う

```mql
expand bus TYPE NAME REVISION;
```

## 2. 基本構文
```mql
expand bus <TYPE> <NAME> <REVISION> [オプション];
```
- `bus`：対象となるビジネスオブジェクトを指定。
- `RELATIONSHIP` を指定して特定の関係のみを展開することも可能。

## 3. 主なオプションと機能
| オプション | 概要 | 例・詳細 |
|------------|------|----------|
| `from relationship <REL>` | 指定したリレーションを「起点から」辿って展開 | `expand bus "${ID}" from relationship "${WS_VAULT}" select bus id dump | where '((owner == "${USER}") || (current.access[read] == true))'` |
| `select <式>` | 取得する属性や関係情報を選択。`print` 同様に使用 | `expand bus '${ID}' from relationship '${EBOM_REL}' terse select relationship id dump |` |
| `where '<条件>'` | 条件を満たす関連オブジェクトのみ展開 | 上記 TMCFolders 例の where 条件 |
| `dump` / `|` | 区切り文字で出力。`|` はパイプのデフォルト区切り | TMCFolders 例で `dump |` を使用 |
| `terse` | 出力を簡略表示 | ENCBOMList 例で `terse` 使用 |
| `structure` | 展開結果を構造として保存・再利用 | 一度保存した構造は `list structure` や `print structure` で再利用可能 |
| `select history` | 履歴情報の取得も可能（select 句で指定） | MQL の select 句で履歴情報も取得できる |

## 4. 実行例
1. **基本展開**  
   ```mql
   expand bus Part A 1;
   ```
   指定部品 A のリレーション構造をデフォルト方向（from/to 両方）で展開。

2. **関係を限定して展開**  
   ```mql
   expand bus "${ID}" from relationship "${WS_VAULT}" select bus id dump | where '((owner == "${USER}") || (current.access[read] == true))';
   ```
   - 指定ボールト関係からのみ展開  
   - `select bus id` で子オブジェクト ID を取得

3. **BOM（部品表）展開**  
   ```mql
   expand bus '${ID}' from relationship '${EBOM_REL}' terse select relationship id dump |;
   ```
   - EBOM リレーションのみ展開  
   - `terse` で簡潔出力、`select relationship id` で接続 ID を取得

4. **保存済み構造の再利用**  
   ```mql
   expand bus Part A 1 structure "MyStruct";
   list structure;
   print structure "MyStruct";
   ```
   展開結果を構造として保存後、`print structure` で再利用

## 5. パフォーマンスと補足
- 複雑な expand は SQL ジョインが増え遅くなるため、インデックスを定義すると高速化できる。
- expand で履歴を取得する際は、必要に応じて MQL で履歴記録を無効化し性能を確保できる。

## 6. まとめ
expand コマンドは 3DEXPERIENCE のビジネスオブジェクト間の関係を把握するうえで不可欠。  
`from/to` や `select`・`where`・`structure` などのオプションを組み合わせ、必要な情報だけを効率的に取得できる。  
適切なインデックス設計や履歴管理を行うことで、複雑な展開処理でも性能と可読性を維持可能である。

