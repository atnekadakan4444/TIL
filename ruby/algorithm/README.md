# 目次
- [`chars` : 文字列を配列に一文字ずつ要素として格納する](#`chars` : 文字列を配列に一文字ずつ要素として格納する)
- [Usage](#usage)
- [License](#license)

# `chars` : 文字列を配列に一文字ずつ要素として格納する
* 要素に分割するには `split` がメジャーだが、文字列に関しては `chars` が使える
* メソッド名的にも `chars` の方が直感的で分かりやすい

```ruby
str = "abcdeあいうえお"

str.split("")   #=> ["a", "b", "c", "d", "e", "あ", "い", "う", "え", "お"]
str.chomp.chars #=> ["a", "b", "c", "d", "e", "あ", "い", "う", "え", "お"]
```

# `while` : 式修飾子(while修飾子、unitl修飾子)を使った繰り返し処理
* ブロック構文を使用しなくとも表現できる制御構文は多い
* メジャーな `if` 意外にも `while` もまた一行で記述可能

```ruby
n = 7
n = '0' + n.to_s while n.to_s.length < 3

p n #=> "007"
```

### 関連リンク
* https://www.javadrive.jp/ruby/for/index12.html
* https://paiza.jp/works/mondai/c_rank_level_up_problems/c_rank_string_step4
