# 目次
- [文字列を配列に一文字ずつ要素として格納する（chars）](#文字列を配列に一文字ずつ要素として格納する（chars）)
- [式修飾子(while修飾子、unitl修飾子)を使った繰り返し処理](#式修飾子(while修飾子、unitl修飾子)を使った繰り返し処理)
- [文字列の右詰め（rjust）](#文字列の右詰め（rjust）)

---

# 文字列を配列に一文字ずつ要素として格納する（chars）
* 要素に分割するには `split` がメジャーだが、文字列に関しては `chars` が使える
* メソッド名的にも `chars` の方が直感的で分かりやすい

```ruby
str = "abcdeあいうえお"

str.split("")   #=> ["a", "b", "c", "d", "e", "あ", "い", "う", "え", "お"]
str.chomp.chars #=> ["a", "b", "c", "d", "e", "あ", "い", "う", "え", "お"]
```

---

# 式修飾子(while修飾子、unitl修飾子)を使った繰り返し処理
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

---

# 文字列の右詰め（rjust）
* 文字数と詰める文字が指定できるので楽
* 破壊的メソッドでは無いので代入は必要

```ruby
str = "123"
rjust_str = str.to_s.rjust(5, "0")

puts str       #=> 123
puts rjust_str #=> 00123
```

### 関連リンク
* https://docs.ruby-lang.org/ja/latest/method/String/i/rjust.html
* https://paiza.jp/works/mondai/c_rank_level_up_problems/c_rank_string_step6/edit?language_uid=ruby&t=14124560d8a752f2e92186b6b9344e0d

---


