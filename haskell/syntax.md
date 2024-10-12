## コメント
- 行コメント: `--`
- ブロックコメント: `{-` から `-}`
    - ネスト可
```haskell
-- ここは行コメント
main = putStrLn "hello, world"  -- ここも行コメント
{- ブロックコメントは
    ネストさせてもよい
-}
```

## ブロック
- 複数の式をまとめて1つの式にする構文
- ブロックは必ず do のようなキーワードと一緒に使う
    - ブロックを導入するキーワードとして，do のほかには where, let, of がある
```haskell
main = do { putStrLn "hello" ; putStrLn "world" ; putStrLn "goodbye" }
```

- ブロック内の式をすべて同一の行に書く場合には，最初と最後の波括弧 {, } は省略できる
```haskell
main = do putStrLn "hello" ; putStrLn "world" ; putStrLn "goodbye"
```

### レイアウト
- ブロック { …​ } は，レイアウト（layout）を利用して見やすく書き換えることができる
- レイアウトは，インデントのレベルによってブロックの範囲を表す仕組み
- インデントの基準は，キーワード do, where, let, of の後に現れる最初の式によって決められる
```haskell
main = do
    putStrLn "hello"
    putStrLn "world"
    putStrLn "goodbye"
```

## 変数
- 変数の定義は次のように書く
- 変数名 = 式
- 同名の変数の再定義は許されません
```haskell
m = 123   -- 変数 n を値 123 に束縛
n = 456   -- 変数 n を値 456 に束縛
main = do
    print m  -- 出力: 123
    print n  -- 出力: 456
```

- 変数の束縛は，必ずしもその変数を使う場所より前に書く必要はありません
```haskell
main = do print m  -- 出力: 123
          print n  -- 出力: 456

m = 123
n = 456
```

- パターンによる束縛
```haskell
(a, b, c) = (123, 3.14, "hello")
main = do
    print a  -- 出力: 123
    print b  -- 出力: 3.14
    print c  -- 出力: "hello"
```
- パターンによる色々な束縛
```haskell
(a, b, c) = (123, 3.14, "hello")
[a, b, c] = [1, 2, 3]
Just x = Just 123
(a, _, _) = (123, 3.14, "hello")
```
- アズパターン
```haskell
t@(a, b, c) = (123, 3.14, "hello")
main = do
    print a  -- 出力: 123
    print b  -- 出力: 3.14
    print c  -- 出力: "hello"
    print t  -- 出力: (123, 3.14, "hello")
```

## 関数
- 関数定義は次のように書く
- 関数名 引数1 ... 引数n = 式
- 関数適用も同様に，引数を空白で区切って並べ，次のように書く
- 関数名 引数1 ... 引数n
```haskell
add x y = x + y
main = print (add 2 3)  -- 出力: 5
```

## 型注釈
```haskell
m, n :: Int  --  "変数 m, n の型は Int" と宣言
m = 1 + 2
n = 2 + 3

main = do print m
          print n
```

- 式にも可能
```haskell
n = 2 + 3 :: Int  --  "式 2 + 3 の型は Int"
```

## 多相型
- ジェネリクス
- a と b は "型変数" あるいは "型パラメータ" と呼ばれる
```haskell
fst (123, 456)      -- 値: 123
fst ("abc", "def")  -- 値: "abc"
fst (123, "abc")    -- 値: 123

Prelude> :t fst
fst :: (a, b) -> a
```

## case式
```haskell
getValue defval maybe =
  case maybe of
    Nothing -> defval
    Just x  -> x

main = do
  print (getValue 0 Nothing)   -- 出力: 0
  print (getValue 0 (Just 5))  -- 出力: 5
```

### ガード
- パターンマッチングに加えて条件式で式の評価を制御する仕組み
- otherwiseはどんな条件でも成立する条件式
    - otherwiseはTrueと等価
```haskell
absMaybe x =
  case x of
    Nothing            -> 0
    Just x | x < 0     -> -x   -- Just x にマッチし，x < 0 のとき
           | otherwise -> x    -- Just x にマッチし，x < 0 でないとき

main = do
  print (absMaybe Nothing)      -- 出力: 0
  print (absMaybe (Just 5))     -- 出力: 5
  print (absMaybe (Just (-5)))  -- 出力: 5
```

## if式
- if式なので値を返す
    - 式1と式2を省略することはできない
    - 式1と式2は型が同じでなければならない
```haskell
abs x = if x < 0 then -x else x

main = print (absolute (-5))  -- 出力: 5
```

## let式
- let { 宣言1 ; ... ; 宣言n } in 式
- 宣言iで定義した変数または関数は，変数i+1以降と式にのみスコープを持つ
    - そして，式がこのlet式全体の値となる
```haskell
area r =
  let pi = 3.14
      square x = x * x
  in pi * square r

main = print (area 10.0)  -- 出力: 314.0
```

## where節
- let式以外で変数や補助関数を局所的に定義する方法
- where { 宣言1 ; ... ; 宣言n }
- 次のプログラムは，先と同様の関数 area を where 節を用いて書き換えたもの
```haskell
area r = pi * square r
  where
    pi = 3.14
    square x = x * x

main = print (area 10.0)  -- 出力: 314.0
```

## 識別子
- 小文字で始まる変数識別子と大文字で始まる構成子識別子という2種類の識別子を使い分けます
