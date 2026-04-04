# Type（型）

## 概要
データの表現・制約・メモリ管理・振る舞いを決定するもの

---

## 1. 値型 (Value Types)
値型は、データそのものを変数に格納します。  
変数のコピー時にはデータそのものが複製されます。

### 基本型（Primitive Types）
言語標準で定義されている、データの最小単位となる型です。

| 分類 | 型名 | 用途・特徴 |
| :--- | :--- | :--- |
| **整数** | `int`, `long`, `byte` | ループのカウント、ID、バイナリデータ |
| **浮動小数点** | `double`, `float` | 科学計算、ゲームの座標（計算速度重視） |
| **高精度小数** | `decimal` | **金額計算、税計算**（誤差を許容しない） |
| **論理** | `bool` | `true` / `false` の条件判定 |
| **文字** | `char` | 1文字（Unicode）の表現 |

**Point**
- 浮動小数点（float/double）は0.1+0.2=0.30000000000000004となり誤差がうまれる

---

### 列挙型（enum）
「状態」や「種類」など、あらかじめ決められた選択肢に名前をつけて管理する型です。  

**役割:**
- 意味のない数値（マジックナンバー）を排除し、コードの意図を明確にする。 |

**例:**
```csharp
public enum UserStatus {
    Registration, // 0: 登録中
    Active,       // 1: 有効
    Banned        // 2: 停止
}

```
---

### 構造体型（struct）
複数のデータを一つにまとめるための軽量な型です。

**役割:**
- 独自のデータ型を定義することで関連する情報の管理を用意にする。

```csharp
// 構造体 (struct) の例
public struct Point
{
    public int X;
    public int Y;
}

```
---

## 2.　参照型
変数にはデータへの「参照（アドレス）」のみが格納されます。  
実際のデータはヒープメモリに置かれ、コピー時にはアドレスだけが複製されます。

### クラス(class)
オブジェクト指向プログラミングの基本単位です。

**例:**
- **string:** 文字列を表す特別な参照型。不変
- **object:** すべての型のルートクラス
- **自作クラス:** 業務ロジックやエンティティを定義します。

### インターフェース (interface)
実装を持つことを保証する「契約」です

### デリゲート (delegate)
メソッドを参照するための型です

### レコード (record)
すべての項目（プロパティ）の値が同じなら同じものとみなす(等価性)
という性質を持つ型

---

## 3. その他

### ジェネリクス (Generics)
特定の型に依存しない「汎用的な枠組み」を定義します。コンパイル時に型が決定されるため、型安全（Type Safe）であり、パフォーマンスも高いのが特徴です。
csharp

```csharp
// 汎用的なレスポンスクラスの例
public class Result<T> 
{
    public bool Success { get; set; }
    public T Data { get; set; } // どんな型でも入れられる
    public string Message { get; set; }
}

// 利用例
var userResult = new Result<User> { Data = new User { Name = "Taro" }, Success = true };
var intResult = new Result<int> { Data = 100, Success = true };
```

---

### ヌル許容型 (Nullable Types)
「値が存在しない（null）」状態を許容します。データベースのフィールド（必須ではない項目）を扱う際に汎用的に使用されます。

```csharp
// 値型に null を許容させる
int? age = null; 

// 汎用的な null チェックパターン
if (age is null) 
{
    Console.WriteLine("年齢が登録されていません。");
}

// Null合体代入演算子 (C# 8.0+)
age ??= 20; // ageがnullなら20を代入
```

---

### ボックス化と型判定
object 型を使うことで、あらゆる型を抽象化して扱うことができますが、元の型に戻す（ダウンキャスト）際には注意が必要です。

```csharp
object genericData = "Hello World";

// 現代的な型判定 (型パターンマッチング)
if (genericData is string text) 
{
    // text 変数として安全に使用可能
    Console.WriteLine($"文字列の長さ: {text.Length}");
}
```

---

### Point
- 型安全性: object で何でも受けるのではなく、なるべく Generics を使って「何が入るか」を明確にすること。
- パフォーマンス: ボックス化（値型を object に入れる）は、大量のデータを扱う際にメモリ消費とCPU負荷を高める原因になること。
- モダンな記述: as によるキャストよりも、is を使ったパターンマッチングの方が安全で読みやすいこと。

---

## 4. 型変換
型同士を変換する手法

### キャスト (Cast)
ある型を別の型として扱います。

**特徴**
- **暗黙的な変換:** データ損失がない場合（int → double など）は自動で行われます。
- **明示的な変換 (キャスト):** データ損失の可能性がある場合、明示的に指定します。

```csharp
double pi = 3.14;
int roundedPi = (int)pi; // 明示的キャスト（小数点以下が切り捨てられる）
```

---

### 安全な型変換（AS）
キャスト失敗による例外（InvalidCastException）を防ぐための手法

```csharp
object obj = "文字列";
string str1 = (string)obj;  // 成功: "文字列"、失敗時: 例外
string str2 = obj as string; // 成功: "文字列"、失敗時: null
```

---



## 5. メモリ管理

- スタック (Stack): 高速に割り当て・解放が行われる。値型が格納される。
- ヒープ (Heap): ガベージコレクション (GC) によって管理される。参照型の実体が格納される。
