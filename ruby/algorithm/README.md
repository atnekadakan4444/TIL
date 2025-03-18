# 目次
- [文字列を配列に一文字ずつ要素として格納する（chars）](#文字列を配列に一文字ずつ要素として格納するchars)
- [式修飾子(while修飾子、until修飾子)を使った繰り返し処理](#式修飾子while修飾子until修飾子を使った繰り返し処理)
- [文字列の右詰め（rjust）](#文字列の右詰めrjust)
- [単なるカウントではなく一致判定カウント（count）](#単なるカウントではなく一致判定カウントcount)
- [簡単に並び替えをしてくれる（sort）](#簡単に並び替えをしてくれるsort)

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

# 式修飾子(while修飾子、until修飾子)を使った繰り返し処理
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

# 単なるカウントではなく一致判定カウント（count）
* レシーバーである配列の要素数をカウントするのが一般的な使い方
* 引数を渡せば `==` の一致判定をした上でカウントする

```ruby
N, M, K = gets.chomp.split(" ").map(&:to_i)
a_ary = Array.new(N)

N.times do |i|
    a_ary[i] = gets.chomp.split(" ").map(&:to_i)
end

a_ary.each do |numbers|
    score = numbers.count(K)
    puts score
end
```

### 関連リンク
* https://docs.ruby-lang.org/ja/latest/method/Array/i/count.html
* https://paiza.jp/works/mondai/c_rank_level_up_problems/c_rank_for_boss

---

# 簡単に並び替えをしてくれる（sort）
* `<=>`演算子を利用した並び替えをやってくれる便利なメソッド
* `self!`であれば破壊的にソートしてくれる
* ブロックを渡せばソート内容を指定できる
    * サンプルのように文字列として比較するか数値として比較するかでは結果が異なる
* `sort_by` という評価判定を指定するメソッドもあるがこちらは「レシーバーが配列で指定の要素同士を比較したい」場合など少し複雑なケースで有効

```ruby
ary1 = [ "d", "a", "e", "c", "b" ]
p ary1.sort                             #=> ["a", "b", "c", "d", "e"]

ary2 = ["9", "7", "10", "11", "8"]
p ary2.sort                             #=> ["10", "11", "7", "8", "9"] (文字列としてソートするとこうなる)
p ary2.sort{|a, b| a.to_i <=> b.to_i }  #=> ["7", "8", "9", "10", "11"] (ブロックを使って数字としてソート)
```

## 文字列での比較の場合だと『辞書順』になる
* 文字列をソートするとき、sortメソッドは要素を文字列として**辞書順（アルファベット順）で並べる**
    * このため、"10"は"1"と"0"で始まるから、"2"や"3"よりも前に来ることになる
* 具体的には、文字列の比較は各文字をUnicode（文字コード）で比較して行われるため、"1"（Unicodeコードが49）が"2"（コードが50）よりも小さいので、"10"は"2"や"3"よりも前に来る
    * 文字コードで見るため索引順というイメージ

```ruby
ary2 = ["111", "3", "10", "11", "1", "2"]
p ary2.sort                             #=> ["1", "10", "11", "111", "2", "3"] (辞書順)
p ary2.sort{|a, b| a.to_i <=> b.to_i }  #=> ["1", "2", "3", "10", "11", "111"] (数値の大小)
```


### 関連リンク
* https://docs.ruby-lang.org/ja/latest/method/Array/i/sort.html
* https://docs.ruby-lang.org/ja/latest/method/Enumerable/i/sort_by.html
* https://paiza.jp/works/mondai/c_rank_level_up_problems/c_rank_sort_step1

---


