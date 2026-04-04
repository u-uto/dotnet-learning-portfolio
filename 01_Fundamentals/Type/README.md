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
「何ができるか」という機能の契約を定義します。  
具体的な中身は持たず、継承先で必ず実装することを保証します。

**役割:**
- 異なるクラスに共通の動作を持たせ、依存性を下げる（疎結合にする）。
```csharp
public interface ILogger {
    void Log(string message); // 実装をもたないメソッドの定義
}

public class ConsoleLogger : ILogger {
    public void Log(string message) => Console.WriteLine($"[Log]: {message}");
}
```

### デリゲート (delegate)
「メソッドを代入できる変数」のような型です。  
イベント処理や、メソッドを引数として渡したい時に使います。

**役割:**
- 実行する処理を後から差し替え可能にする。
```csharp
// 文字列を受け取り、戻り値がないメソッドを指す型を定義
public delegate void Notify(string message);

public class Process {
    public void Start(Notify callback) {
        Console.WriteLine("処理開始...");
        callback("完了しました！"); // 渡されたメソッドを実行
    }
}

// 利用例：ラムダ式を使ってその場で処理を渡す
var p = new Process();
p.Start(msg => Console.WriteLine(msg));
```

### レコード (record)
値の等価性（すべての項目（プロパティ）の値が同じなら同じものとみなす）という性質を持つ型

**役割:**
- データの塊（DTOなど）を不変（Immutable）に保ちたい場合に最適です。

```csharp
// 1行で定義可能（プロパティが自動生成される）
public record UserRecord(int Id, string Name);

var u1 = new UserRecord(1, "田中");
var u2 = new UserRecord(1, "田中");

Console.WriteLine(u1 == u2); // True (参照先が違っても、中身が同じならTrue)
// 非破壊的書き換え (Nameだけ変えた新しいコピーを作る)
var u3 = u1 with { Name = "鈴木" };
```

---

## 3. その他

### ジェネリクス (Generics)
特定の型に依存しない「汎用的な枠組み」を定義します。コンパイル時に型が決定されるため、型安全（Type Safe）であり、パフォーマンスも高いのが特徴です。

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
値型（スタック）を object 型（ヒープ）に変換する操作です。

```csharp
int num = 123;          // 値型 (スタック)

// ボックス化 (Boxing)
object boxed = num;     // intをobjectという「箱」に入れる (ヒープへ移動)

// ボックス化解除 (Unboxing)
int unboxed = (int)boxed; // 明示的なキャストが必要
```

**注意点:** ボックス化はメモリの割り当て（アロケーション）が発生するため、ループ内で数万回行うような処理ではパフォーマンス低下の原因になります。

---

## 4. 型変換(Type Conversion)

### キャスト (Cast)
ある型を別の型として扱います。

### 暗黙的変換
互換性のある型間（int → long/double）では自動変換されます。
```csharp
int x = 10;
long y = x;  // 自動変換
```

### 明示的変換（キャスト）
データ損失や非互換型の場合は()で強制して変換

```csharp
double d = 3.99;
int i = (int)d;  // 3（小数点切り捨て）
```

**※変換失敗時はInvalidCastExceptionが発生します。**

### 安全な型変換
キャスト失敗による例外を防ぐための手法

```csharp
object obj = "C# Learning";

// 1. as演算子
string? s = obj as string;
if (s != null) {
    Console.WriteLine(s.Length);
}

// 2. is演算子 (型パターンマッチング: 現代のC#で最も推奨される書き方)
if (obj is string text) {
    // このブロック内では text 変数が string として使える
    Console.WriteLine(text.Length);
}
```

---

## 5. メモリ管理

- スタック (Stack): 高速に割り当て・解放が行われる。値型が格納される。
- ヒープ (Heap): ガベージコレクション (GC) によって管理される。参照型の実体が格納される。

| 領域 | 特徴 | 格納される主な型 |
| :--- | :--- | :--- |
| **スタック (Stack)** | 高速・軽量。スコープを抜けると即座に解放。 | 値型 (`int`, `struct` 等) |
| **ヒープ (Heap)** | 柔軟・大容量。ガベージコレクション (GC) が管理。 | 参照型 (`class`, `record` 等) |