2章 Rustツアーメモ
---

- rustup インストーラ
- cargo コンパイルマネージャーかつパッケージマネージャー。テストも言語に組み込まれてるから実行できる。

**パッケージディレクトリを作成**

```
cargo new hello //作成
cargo clean //削除
```

---

- fn //関数定義
- 整数型の名前: サイズと符号の有無を反映  
  u64 unsined 64byte  
  f32/f64 浮動小数点
- let //ローカル変数宣言（可変の意味はない／JSとは違うね）
  - mut //mutable 可変変数宣言
- panic
    - Karnel panic みたいなもの

---

- #[test]マーカ テストであることを示すattribute
    - コンパイラが認識するためのもの

---

- トレイトは型が実装できるめメソッドの集合
    - 自分で作った型に生やす事もできる
    - その場合実装は自分でやる（コンパイルで止められる）
- Vec サイズ可変の配列

- Result 失敗する可能性がある関数が返す型
    - OK(v) パース成功、vを返す
    - Err(e) パース失敗、eはエラー値
    - expectでエラー時の挙動を決められる

- ベクタは可変長のためどこまで長くなるかわからない＝Rustが慎重に取り扱う値
- `&x` xへの参照を借用するもの
- `*r` 参照rの参照先の値

---
---
2022/10/23
クレート入れてWebページを作る

- crate クレート
    - Rustのパッケージ。ライブラリ・実行ファイル。Rubyにおけるgem的なもの
      `

```toml
[dependencies]
actix-web = "1.0.8"
serde = { version = "1.0", features = ["derive"] }
```

featureなかったら全部とはならないのかな？
→ featuresなくても普通に cargo run で動くので、大丈夫そう
取り込む対象削ったほうが全体的に軽くなるからかな？

**Rustのクロージャ式**
関数のように呼び出すことのできる値
JSの var xx = () => {} みたいなものかな。関数の引数としても取れる

```rust
 (| | {});
```

**raw string構文**  
Rubyのヒアドキュメントみたいなもの
``` r#" "#``` で囲む
`#` は増やせる。中で#使ってるとそこで止まるので増やして止まらないようにできる

**serdeクレート👀**

- Deserialize  
  使って型定義すると、POSTリクエストに使用する形式のデータ（Json,Yaml,Tomlなど）から自動で型定義に合わせてoutputしてくれる
- Serialize  
  Deserializeの逆

# 2.6 並列プログラミング

num::Complex 複素数を管理する型・多分もう出てこない

///ドキュメントコメント  
ドキュメント自動生成してくれる

ジェネリック構造体  
すべての型Tに対して、みたいな使い方する

```rust
Complex<T> { re: T, im: T }
```

Option型  
列挙型

```rust
enum Option<T> {
    Some < T>,
None
}

-> Option<f64>
//みたいな定義のしかたする
```

ジェネリック関数
T はFromStrを持っているとして定義
関数仕様時に基本的にはTの定義も一緒にする（generic型の場合は類推可能なので、その場合は省略可能）

```rust
use std::str::FromStr;

fn parse_pair<T: FromStr>(s: &str, separator: string) -> Option<(T, T)> {
    match s.find(separator) {
        None => None,
        Some(index) => {
            match (T::from_str(&s[..index]), T::from_str(&s[index + 1..])) {
                (Ok(l), Ok(r)) => Some(l, r),
                _ => None
            }
        }
    }
}
```

渡した文字列sから指定したパラメーターまでの場所をfindで特定
あればsomeで返却値を受け取って、セパレーター前半のものをstrに変換、後半のものをsrtに変換
それぞれの返り値がResult型になるので、きちんと帰ってきたときだけSomeで返すし、そうでなければNoneで返す
_はワイルドカード

タプル
CやJavaの配列は中身が同一の型であるもの
タプルは配列の中身の型が複数のもの

Someの返り値がTで定義された型が2つはいった配列

```rust
Option<(T, T) >
```

---
2022/10/29
`target/release/mandelbrot mandel.png 4000x3000 -1.20,0.35 -1,0.20  4.48s user 0.11s system 71% cpu 6.400 total`

- unwrap
expect の不親切版
